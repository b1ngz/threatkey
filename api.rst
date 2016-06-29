.. _api:

API接口
=================

用户可以调用系统提供的API获取部署蜜罐的日志和样本

日志获取说明
---------------

1. 用户使用user_token获取所有的节点信息
2. 根据节点信息中的node_token，获取节点下所有的蜜罐信息
3. 根据蜜罐信息中的vid，日期范围，日志类型获取相应日志

API说明
---------------

获取用户所有的节点(Node)信息

url: http://www.threatkey.com/api/user/getAllNode
 
method: POST

请求参数::

    {
      "user_token": "xxxxxxxxxxxxxxx"
    }

字段说明:

- user_token(required)：用户token，请求令牌

token获取说明:

登陆后右上角点击我的资料，页面中的Token项即为 user_token

返回值::

    {
      "ret": 1,
      "data": [
        {
          "node_name": "aws_54.223.43.5",
          "node_token": "9d688986c10876cb79ddb419f396ca43"
        },
        {
          "node_name": "us_159.203.231.204",
          "node_token": "c8c9e85c0ea03156c53b27616887496d"
        }
      ],
      "msg": ""
    }

获取节点内所有漏洞环境

url: http://www.threatkey.com/api/user/getAllEnv

method: POST

请求参数::

    {
      "user_token": "xxxxx",
      "node_token": "xxxxxx"
    }
    
字段说明:

user_token(required)：用户token，请求令牌

node_token(required)：节点token

返回值::

    {
      "ret": 1,
      "data": [
         {
           "vid": "f705ab2e2d7c8479",
           "status": "3",
           "type_id": "5",
           "ports": [
             "22",
             "6379"
           ],
          "type_name": "Redis_SSH"
        },
        {
          "vid": "1b3c9baa28e3e767",
          "status": "1",
          "type_id": "2",
          "ports": ["23"],
          "type_name": "Telnet"
        }
      ],
      "msg": ""
    }
    
参数来源：

node_token： 获取用户所有的节点信息 API请求返回节点信息中的 node_token 字段

字段说明：
- vid: 蜜罐唯一id
- status：蜜罐状态，对应关系见后
- type_id: 蜜罐类型id
- ports: 蜜罐使用端口
- type_name: 蜜罐类型名称

节点内蜜罐信息status 字段值说明：

- 0：停止
- 1：运行
- -1：部署失败
- 3：等待创建

获取得蜜罐日志

url: http://www.threatkey.com/api/user/getEnvScrollLogs

method: POST

请求参数：

- type(required): 日志类型
- type_id(required): 蜜罐唯一 ID， 16位字符串
- start_time: 日志起始日期。 格式 2016-06-23 19:37:20
- end_time: 结束日期。值说明同参数start_time
- scroll_id: 请求下一页数据用

返回值::

    {
      "ret": 1,
      "data": {
        "scroll_id": "cXVlcnlUaGVuRmV0Y2g7NTs1MjYzNjp1NVluMmxCMlJrbWVrZ3JHOFV5YjZROzUyNjM4OnU1WW4ybEIyUmttZWtnckc4VXliNlE7NTI2Mzk6dTVZbjJsQjJSa21la2dyRzhVeWI2UTs1MjY0MDp1NVluMmxCMlJrbWVrZ3JHOFV5YjZROzUyNjM3OnU1WW4ybEIyUmttZWtnckc4VXliNlE7MDs=",
        "infos": [
          {
            "_index": "honeypot_geo",
            "_type": "dns",
            "_id": "AVV0DpM0kSuo1eK3YpRa",
            "_score": null,
            "_source": {
              "vid": "7ccea49db5e5c97b",
              "domain": "ec2-54-254-169-195.ap-southeast-1.compute.amazonaws.com",
              "address": "54.254.169.195",
              "time": "2016-06-22T01:42:37.670320+0800",
              "src_ip": "159.203.231.204",
              "src_geoip": {
                "country_name": "United States",
                "continent_code": "NA",
                "city_name": "San Francisco",
                "latitude": 37.7749,
                "longitude": -122.4194
              }
            },
            "sort": [
              1466530957670
            ]
          },
          {
            "_index": "honeypot_geo",
            "_type": "dns",
            "_id": "AVV0DpM0kSuo1eK3YpRZ",
            "_score": null,
            "_source": {
              "vid": "7ccea49db5e5c97b",
              "domain": "ec2-54-254-169-195.ap-southeast-1.compute.amazonaws.com",
              "address": "54.254.169.195",
              "time": "2016-06-22T01:42:37.590557+0800",
              "src_ip": "159.203.231.204",
              "src_geoip": {
                "country_name": "United States",
                "continent_code": "NA",
                "city_name": "San Francisco",
                "latitude": 37.7749,
                "longitude": -122.4194
              }
            },
            "sort": [
              1466530957590
            ]
          }
        ]
      },
      "msg": ""
    }
    
字段说明：

- type: 可选类型包括 irc, file, http_out, dns, bash, access ，只能选择一个
- vid：获取节点内所有漏洞环境 API请求返回漏洞环境信息中的 vid 字段
- scroll_id：返回结果中的scroll_id，具体使用方法可参考elasticsearch文档

type类型说明

- irc: irc协议控制端发送的数据包信息
- file: 文件变化
- http_out: 向外发出的http(s)请求
- dns: 向外发出的dns请求
- bash: ssh/telnet服务执行的bash命令日志
- access: 非web服务的访问日志，service字段来区分服务，如redis

请求下一页数据说明：

只需将每次返回结果中的scroll_id，传给接口就行，其它参数无需再传
当scroll_id过期时，需重新请求接口获取新的scroll_id
获取得蜜罐日志方案二

url: http://www.threatkey.com/api/user/getEnvLogs

method: POST

请求参数：
- type(required): 日志类型
- type_id(required): 蜜罐唯一 ID， 16位字符串
- start_time: 日志起始日期。 格式 2016-06-23 19:37:20
- end_time: 结束日期。值说明同参数from
- page: 页数

返回值::

    {
      "ret": 1,
      "data": {
        "current_page": 1,
        "total_page": 738,
        "infos": [
          {
            "_index": "honeypot_geo",
            "_type": "dns",
            "_id": "AVV0DpM0kSuo1eK3YpRa",
            "_score": null,
            "_source": {
              "vid": "7ccea49db5e5c97b",
              "domain": "ec2-54-254-169-195.ap-southeast-1.compute.amazonaws.com",
              "address": "54.254.169.195",
              "time": "2016-06-22T01:42:37.670320+0800",
              "src_ip": "159.203.231.204",
              "src_geoip": {
                "country_name": "United States",
                "continent_code": "NA",
                "city_name": "San Francisco",
                "latitude": 37.7749,
                "longitude": -122.4194
              }
            },
            "sort": [
              1466530957670
            ]
          },
          {
            "_index": "honeypot_geo",
            "_type": "dns",
            "_id": "AVV0DpM0kSuo1eK3YpRZ",
            "_score": null,
            "_source": {
              "vid": "7ccea49db5e5c97b",
              "domain": "ec2-54-254-169-195.ap-southeast-1.compute.amazonaws.com",
              "address": "54.254.169.195",
              "time": "2016-06-22T01:42:37.590557+0800",
              "src_ip": "159.203.231.204",
              "src_geoip": {
                "country_name": "United States",
                "continent_code": "NA",
                "city_name": "San Francisco",
                "latitude": 37.7749,
                "longitude": -122.4194
              }
            },
            "sort": [
              1466530957590
            ]
          }
        ]
      },
      "msg": ""
    }
