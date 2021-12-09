---
Exercise:
    title: '模块 01 第 8 单元 - 使用全局虚拟网络对等互连连接两个 Azure 虚拟网络'
    module: '模块 - Azure 虚拟网络简介'
---
# 模块 01 第 8 单元 - 使用全局虚拟网络对等互连连接两个 Azure 虚拟网络

## 练习场景 
在本单元中，你将添加对等互连来允许流量流，从而配置 CoreServicesVnet 和 ManufacturingVnet 之间的连接性。 

在本单元中，你将：

+ 任务 1：创建虚拟机来测试配置
+ 任务 2：使用 RDP 连接到测试 VM
+ 任务 3：测试 VM 之间的连接
+ 任务 4：在 CoreServicesVnet 与 ManufacturingVnet 之间创建 VNet 对等互连
+ 任务 5：测试 VM 之间的连接
+ 任务 6：清理资源

## 任务 1：创建虚拟机来测试配置

在本部分中，你将在 Manufacturing VNet 上创建一个测试 VM，来测试你能否从 ManufacturingVnet 访问其他 Azure 虚拟网络中的资源。

### 创建 ManufacturingVM

1. 在 Azure 主页上，使用全局搜索类型“**虚拟机**”并选择服务下的虚拟机。

2. 在虚拟机中，选择“**+ 创建; + 虚拟机**”。

3. 使用下表中的信息创建 VM。

| **选项卡**         | **选项**                                                   | **值**                             |
| --------------- | ------------------------------------------------------------ | ------------------------------------- |
| 基本          | 资源组                                               | ContosoResourceGroup                  |
|                 | 虚拟机名称                                         | ManufacturingVM                       |
|                 | 区域                                                       | 西欧                           |
|                 | 可用性选项                                         | 无需基础结构冗余 |
|                 | 映像                                                        | Windows Server 2022 Datacenter- Gen1  |
|                 | Azure Spot 实例                                          | 未选择                          |
|                 | 大小                                                         | Standard_D2s_v3 - 2vcpus，8GiB 内存 |
|                 | 用户名                                                     | TestUser                              |
|                 | 密码                                                     | TestPa$$w0rd!                         |
|                 | 公共入站端口                                         | 允许选定的端口                  |
|                 | 选择入站端口                                         | RDP (3389)                            |
| 磁盘           | 无需任何更改                                          |                                       |
| 网络      | 虚拟网络                                              | ManufacturingVnet                     |
|                 | 子网                                                       | ManufacturingSystemSubnet (10.30.10.0/24)|
|                 | 公共 IP                                                    | （新）ManufacturingVM-ip              |
|                 | NIC 网络安全组                                   | 基本                                 |
|                 | 公共入站端口                                         | 允许选定的端口                  |
|                 | 选择入站端口                                         | RDP (3389)                            |
|                 | 负载均衡                                               | 未选择                          |
| 管理      | 无需任何更改                                          |                                       |
| 高级        | 无需任何更改                                          |                                       |
| 标记            | 无需任何更改                                          |                                       |
| 查看 + 创建 | 检查设置，然后选择“创建”                       |                                       |


4. 部署完成后，选择“**前往资源**”。

## 任务 2：使用 RDP 连接到测试 VM

1. 在 Azure 门户主页上，选择“**虚拟机**”。

2. 选择“**ManufacturingVM**”。

3. 在 ManufacturingVM 中，选择“**连接**” > “**RDP**”。

4. 在 ManufacturingVM | 连接 中，选择“**下载 RDP 文件**”。

5. 将 RDP 文件保存到桌面。

6. 使用 RDP 文件、用户名 **TestUser** 和密码 **TestPa$w0rd!** 连接到 ManufacturingVM。

7. 在 Azure 门户主页上，选择“**虚拟机**”。

8. 选择“**TestVM1**”。

9. 在 TestVM1 中，选择“**连接**” > “**RDP**”。

10. 在 TestVM1 | 连接中，选择“**下载 RDP 文件**”。

11. 将 RDP 文件保存到桌面。

12. 使用 RDP 文件、用户名 **TestUser** 和密码 **TestPa$w0rd!** 连接到 TestVM1。

13. 在两个 VM 上的“**为设备选择隐私设置**” 中，选择“**接受**”。

14. 在两个 VM 上的“**网络**” 中，选择“**是**”。

15. 在 TestVM1 中，打开 PowerShell 提示符，然后运行以下命令：ipconfig

16. 记下 IPv4 地址。 

 

## 任务 3：测试 VM 之间的连接

1. 在 ManufacturingVM 上打开 PowerShell 提示符。

2. 使用以下命令验证 CoreServicesVnet 上没有到 TestVM1 的连接。请确保为 TestVM1 使用 IPv4 地址。

| PowerShell                               |
| ---------------------------------------- |
| Test-NetConnection 10.20.20.4 -port 3389 |


3. 测试连接应该会失败，你将看到如下结果：
   ![Test-NetConnection 10.20.20.4 -port 3389 显示失败的 PowerShell 窗口 ](../media/test-netconnection-fail.png)

 

## 任务 4：在 CoreServicesVnet 与 ManufacturingVnet 之间创建 VNet 对等互连

1. 在 Azure 主页中，选择“**虚拟网络**”， 然后选择“**CoreServicesVnet**”。

2. 在 CoreServicesVnet 中的“**设置**”下，选择“**对等互连**”。
   ![Core Services VNet 对等互连设置的屏幕截图 ](../media/create-peering-on-coreservicesvnet.png)

3. 在 CoreServicesVnet | 对等互连上，选择“**+添加**”。

4. 使用下表中的信息创建对等互连。

| **部分**                          | **选项**                                    | **值**                             |
| ------------------------------------ | --------------------------------------------- | ------------------------------------- |
| 此虚拟网络                 |                                               |                                       |
|                                      | 对等互连链接名称                             | CoreServicesVnet-to-ManufacturingVnet |
|                                      | 流向远程虚拟网络的流量             | 允许（默认）                       |
|                                      | 从远程虚拟网络转发的流量 | 允许（默认）                       |
|                                      | 虚拟网络网关或路由服务器       | 无（默认值）                        |
| 远程虚拟网络               |                                               |                                       |
|                                      | 对等互连链接名称                             | ManufacturingVnet-to-CoreServicesVnet |
|                                      | 虚拟网络部署模型              | 资源管理器                      |
|                                      | 我知道我的资源 ID                         | 未选择                          |
|                                      | 订阅                                  | MOC Subscription-lodxxxxxxxx          |
|                                      | 虚拟网络                               | ManufacturingVnet                     |
|                                      | 流向远程虚拟网络的流量             | 允许（默认）                       |
|                                      | 从远程虚拟网络转发的流量 | 允许（默认）                       |
|                                      | 虚拟网络网关或路由服务器       | 无（默认值）                        |
| 检查设置，然后选择“添加”。 |                                               |                                       |
|                                      |                                               |                                       |


5. 在 CoreServicesVnet | 对等互连中，验证是否列出 **CoreServicesVnet-to-ManufacturingVnet** 对等互连。

6. 在虚拟网络下，选择“**ManufacturingVnet**”，并验证是否列出 **ManufacturingVnet-to-CoreServicesVnet** 对等互连。

 

## 任务 5：测试 VM 之间的连接

1. 在 ManufacturingVM 上打开 PowerShell 提示符。

2. 使用以下命令验证 CoreServicesVnet 上现是否有到 TestVM1 的连接。 

| PowerShell                               |
| ---------------------------------------- |
| Test-NetConnection 10.20.20.4 -port 3389 |


3. 测试连接应该会成功，你将看到如下结果：
   ![Test-NetConnection 10.20.20.4 -port 3389 显示 TCP 测试成功：true 的 Powershell 窗口](../media/test-connection-succeeded.png)

 

恭喜！你已通过添加对等互连成功配置 VNet 之间的连接性。 

## 任务 6：清理资源

   >**备注**： 请记得删除不再使用的所有新创建的 Azure 资源。删除未使用的资源，确保不产生意外费用。

1. 在 Azure 门户中，在 **Cloud Shell** 窗格中打开“**PowerShell**”会话。（如果需要，请使用默认设置创建 Cloud Shell 存储。）

1. 运行以下命令，删除在本模块各个实验室中创建的所有资源组：

   ```powershell
   Remove-AzResourceGroup -Name 'ContosoResourceGroup' -Force -AsJob
   ```

    >**备注**： 该命令以异步方式执行（由 -AsJob 参数决定），因此，虽然你随后可在同一 PowerShell 会话中立即运行另一个 PowerShell 命令，但实际上要花几分钟才能删除资源组。
