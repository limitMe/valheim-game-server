
## 英灵神殿服务器自动搭建 - 阿里云资源编排
**Valheim game server auto deploy - Aliyun ROS**

1. 在相同地域创建好 NAS文件存储NFS卷，专有网络，虚拟交换机，安全组。并且找到它们各自的ID，NFS需要知道它的路径。
1. Create NFS volumes x2, VPC, VSwitch and SecurityGroup in the same zone. Copy their ID. For NFS volumes, copy its url path.



2. 访问所在地域的资源编排ROS，进入`资源栈` `创建资源栈`
2. visit `https://rosnext.console.aliyun.com`, go `Resources Stack` -> `Create Resources Stack`



3. 用步骤1的对应id替换下面代码中`{YourValue}`,复制粘贴到网页中
3. Copy the following json and replace all `{YourValue}` using id from step 1



4. 大功告成。下次启动只需要执行步骤三了。
4. Done. For next start, all you need to do is step 3.

```json
{
  "ROSTemplateFormatVersion": "2015-09-01",
  "Resources": {
    "EIPc2b": {
      "Type": "ALIYUN::VPC::EIP",
      "Properties": {
        "InternetChargeType": "PayByTraffic",
        "Bandwidth": 15,
        "InstanceChargeType": "Postpaid",
        "Name": "valheim-auto"
      },
      "Metadata": {
        "ALIYUN::ROS::Designer": {
          "id": "2adedf77-fa2e-4d6d-91fc-bed0d7fbfa2c"
        }
      }
    },
    "ContainerGroupd4a": {
      "Type": "ALIYUN::ECI::ContainerGroup",
      "Properties": {
        "EipInstanceId": {
          "Fn::GetAtt": [
            "EIPc2b",
            "AllocationId"
          ]
        },
        "Container": [
          {
            "EnvironmentVar": [
              {
                "Value": "2456",
                "Key": "PORT"
              },
              {
                "Value": "{YourValue}",
                "Key": "NAME"
              },
              {
                "Value": "{YourValue}",
                "Key": "WORLD"
              },
              {
                "Value": "LJZZL",
                "Key": "PASSWORD"
              },
              {
                "Value": "1",
                "Key": "PUBLIC"
              }
            ],
            "Image": "mbround18/valheim",
            "VolumeMount": [
              {
                "MountPath": "/home/steam/.config/unity3d/IronGate/Valheim",
                "Name": "world"
              },
              {
                "MountPath": "/home/steam/valheim",
                "Name": "server"
              }
            ],
            "Port": [
              {
                "Protocol": "UDP",
                "Port": 2456
              },
              {
                "Protocol": "UDP",
                "Port": 2457
              },
              {
                "Protocol": "UDP",
                "Port": 2458
              }
            ],
            "Name": "valheim-auto"
          }
        ],
        "SecurityGroupId": "{YourValue}",
        "ContainerGroupName": "valheim-auto",
        "Volume": [
          {
            "NFSVolume.Server": "{YourValue}",
            "NFSVolume.Path": "/",
            "Type": "NFSVolume",
            "Name": "world"
          },
          {
            "NFSVolume.Server": "{YourValue}",
            "NFSVolume.Path": "/",
            "Type": "NFSVolume",
            "Name": "server"
          }
        ],
        "VSwitchId": "{YourValue}",
        "Memory": 8,
        "Cpu": 1
      },
      "Metadata": {
        "ALIYUN::ROS::Designer": {
          "id": "eb77aeb6-340b-48d0-bc52-1820750e0256"
        }
      }
    }
  },
  "Parameters": {},
  "Metadata": {
    "ALIYUN::ROS::Designer": {
      "8ccd7b45-a31d-419a-85b8-80c713157237": {
        "source": {
          "id": "eb77aeb6-340b-48d0-bc52-1820750e0256"
        },
        "z": 1,
        "target": {
          "id": "2adedf77-fa2e-4d6d-91fc-bed0d7fbfa2c"
        }
      },
      "2adedf77-fa2e-4d6d-91fc-bed0d7fbfa2c": {
        "position": {
          "y": 226,
          "x": 418
        },
        "z": 0,
        "size": {
          "width": 60,
          "height": 60
        }
      },
      "eb77aeb6-340b-48d0-bc52-1820750e0256": {
        "position": {
          "y": 226,
          "x": 661
        },
        "z": 0,
        "size": {
          "width": 60,
          "height": 60
        }
      }
    }
  }
}
```