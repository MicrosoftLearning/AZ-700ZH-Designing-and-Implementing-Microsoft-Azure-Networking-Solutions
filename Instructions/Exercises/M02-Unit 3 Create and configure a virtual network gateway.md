---
Exercise:
    title: '模块 02 第 3 单元 - 创建和配置虚拟网络网关'
    module: '模块 - 设计和实现混合网络'
---


# 模块 02 第 3 单元 - 创建和配置虚拟网络网关

在本练习中，你将配置一个虚拟网络网关，来连接 Contoso Core Services VNet 和 Manufacturing VNet。 

在本练习中，你将：

+ 任务 1：创建 CoreServicesVnet 和 ManufacturingVnet
+ 任务 2：创建 CoreServicesTestVM
+ 任务 3：创建 ManufacturingTestVM
+ 任务 4：使用 RDP 连接到测试 VM
+ 任务 5：测试 VM 之间的连接
+ 任务 6：创建 CoreServicesVnet 网关
+ 任务 7：创建 ManufacturingVnet 网关
+ 任务 8：将 CoreServicesVnet 连接到 ManufacturingVnet 
+ 任务 9：将 ManufacturingVnet 连接到 CoreServicesVnet
+ 任务 10：验证连接是否连接 
+ 任务 11：测试 VM 之间的连接

## 任务 1：创建 CoreServicesVnet 和 ManufacturingVnet

1. 在 Azure 门户中，在“**Cloud Shell**”窗格中打开“**PowerShell**”会话。

2. 在“Cloud Shell”窗格的工具栏中单击“上传/下载文件”图标，在下拉菜单中单击“上传”，然后将以下文件上传到 Cloud Shell 主目录中：**azuredeploy.json** 和 **azuredeploy.parameters.json**。

3. 部署以下 ARM 模板来创建本练习所需的虚拟网络和子网：

   ```powershell
   $RGName = "ContosoResourceGroup"
   #create resource group if it doesnt exist
   New-AzResourceGroup -Name $RGName -Location East US
   New-AzResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
   ```

## 任务 2：创建 CoreServicesTestVM

1. 在 Azure 主页上，使用全局搜索类型“**虚拟机**”并选择服务下的虚拟机。

2. 在虚拟机中，选择“**+ 创建; + 虚拟机**”。

3. 使用下表中的信息创建 VM。

   | **选项卡**         | **选项**                                                   | **值**                             |
   | --------------- | ------------------------------------------------------------ | ------------------------------------- |
   | 基本          | 资源组                                               | ContosoResourceGroup                  |
   |                 | 虚拟机名称                                         | CoreServicesTestVM                    |
   |                 | 区域                                                       | 美国东部                               |
   |                 | 可用性选项                                         | 无需基础结构冗余 |
   |                 | 映像                                                        | Windows Server 2022 Datacenter- Gen1  |
   |                 | Azure Spot 实例                                          | 未选择                          |
   |                 | 大小                                                         | Standard_D2s_v3 - 2vcpus，8GiB 内存 |
   |                 | 用户名                                                     | TestUser                              |
   |                 | 密码                                                     | TestPa$$w0rd!                         |
   |                 | 公共入站端口                                         | 允许选定的端口                  |
   |                 | 选择入站端口                                         | RDP (3389)                            |
   | 磁盘           | 无需任何更改                                          |                                       |
   | 网络      | 虚拟网络                                              | CoreServicesVnet                      |
   |                 | 子网                                                       | DatabaseSubnet (10.20.20.0/24)        |
   |                 | 公共 IP                                                    | （新）CoreServicesTestVM-ip           |
   |                 | NIC 网络安全组                                   | 基本                                 |
   |                 | 公共入站端口                                         | 允许选定的端口                  |
   |                 | 选择入站端口                                         | RDP (3389)                            |
   |                 | 负载均衡                                               | 未选择                          |
   | 管理      | 无需任何更改                                          |                                       |
   | 高级        | 无需任何更改                                          |                                       |
   | 标记            | 无需任何更改                                          |                                       |
   | 查看 + 创建 | 检查设置，然后选择“创建”                       |                                       |

4. 部署完成后，选择“**前往资源**”。

## 任务 3：创建 ManufacturingTestVM

1. 在 Azure 主页上，使用全局搜索类型“**虚拟机**”并选择服务下的虚拟机。

2. 在虚拟机中，选择“**+ 创建; + 虚拟机**”。

3. 使用下表中的信息创建 VM。

   | **选项卡**         | **选项**                                                   | **值**                                 |
   | --------------- | ------------------------------------------------------------ | ----------------------------------------- |
   | 基本          | 资源组                                               | ContosoResourceGroup                      |
   |                 | 虚拟机名称                                         | ManufacturingTestVM                       |
   |                 | 区域                                                       | 西欧                               |
   |                 | 可用性选项                                         | 无需基础结构冗余     |
   |                 | 映像                                                        | Windows Server 2022 Datacenter- Gen1      |
   |                 | Azure Spot 实例                                          | 未选择                              |
   |                 | 大小                                                         | Standard_D2s_v3 - 2vcpus，8GiB 内存     |
   |                 | 用户名                                                     | TestUser                                  |
   |                 | 密码                                                     | TestPa$$w0rd!                             |
   |                 | 公共入站端口                                         | 允许选定的端口                      |
   |                 | 选择入站端口                                         | RDP (3389)                                |
   | 磁盘           | 无需任何更改                                          |                                           |
   | 网络      | 虚拟网络                                              | ManufacturingVnet                         |
   |                 | 子网                                                       | ManufacturingSystemSubnet (10.40.40.0/24) |
   |                 | 公共 IP                                                    | （新）ManufacturingTestVM-ip              |
   |                 | NIC 网络安全组                                   | 基本                                     |
   |                 | 公共入站端口                                         | 允许选定的端口                      |
   |                 | 选择入站端口                                         | RDP (3389)                                |
   |                 | 负载均衡                                               | 未选择                              |
   | 管理      | 无需任何更改                                          |                                           |
   | 高级        | 无需任何更改                                          |                                           |
   | 标记            | 无需任何更改                                          |                                           |
   | 查看 + 创建 | 检查设置，然后选择 “**创建**”                   |                                           |

4. 部署完成后，选择“**前往资源**”。

## 任务 4：使用 RDP 连接到测试 VM

1. 在 Azure 门户主页上，选择“**虚拟机**”。
2. 选择“**ManufacturingTestVM**”。
3. 在 **ManufacturingTestVM** 中，选择“**连接” > “RDP**”。
4. 在“**ManufacturingTestVM | 连接**”中，选择“**下载 RDP 文件**”。
5. 将 RDP 文件保存到桌面。
6. 使用 RDP 文件、用户名 **TestUser** 和密码 **TestPa$w0rd!** 连接到 ManufacturingTestVM。
7. 在 Azure 门户主页上，选择“**虚拟机**”。
8. 选择“**CoreServicesTestVM**”。
9. 在 **CoreServicesTestVM** 中，选择“**连接” > “RDP**”。
10. 在“**CoreServicesTestVM | 连接**” 中，选择“**下载 RDP 文件**”。
11. 将 RDP 文件保存到桌面。
12. 使用 RDP 文件、用户名 **TestUser** 和密码 **TestPa$w0rd!** 连接到 CoreServicesTestVM。
13. 在两个 VM 上的 “**为设备选择隐私设置**” 中，选择“**接受**”。
14. 在两个 VM 上的“**网络**”中，选择“**是**”。
15. 在 CoreServicesTestVM 中，打开 PowerShell，然后运行以下命令：ipconfig
16. 记下 IPv4 地址。 

 

## 任务 5：测试 VM 之间的连接

1. 在 **ManufacturingTestVM** 上打开 PowerShell。

2. 使用以下命令验证 CoreServicesVnet 上没有到 CoreServicesTestVM 的连接。请确保为 CoreServicesTestVM 使用 IPv4 地址。

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

3. 测试连接应该会失败，你将看到如下结果：

   ![Test-NetConnection 失败。](../media/test-netconnection-fail.png)

 

##  任务 6：创建 CoreServicesVnet 网关

1. 在“**搜索资源、服务和文档(G+/)**”中，输入“**虚拟网络网关**”，然后从结果中选择“**虚拟网络网关**”。
   ![在 Azure 门户中搜索虚拟网络网关。](../media/virtual-network-gateway-search.png)

2. 在虚拟网络网关中，选择“**+创建**”。

3. 使用下表中的信息创建虚拟网络网关：

   | **选项卡**         | **部分**       | **选项**                                  | **值**                    |
   | --------------- | ----------------- | ------------------------------------------- | ---------------------------- |
   | 基本          | 项目详细信息   | 订阅                                | 无需任何更改          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | 实例详细信息  | 名称                                        | CoreServicesVnetGateway      |
   |                 |                   | 区域                                      | 美国东部                      |
   |                 |                   | 网关类型                                | VPN                          |
   |                 |                   | VPN 类型                                    | 基于路由                  |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | 代系                                  | 第 1 代                  |
   |                 |                   | 虚拟网络                             | CoreServicesVnet             |
   |                 |                   | 子网                                      | GatewaySubnet (10.20.0.0/27) |
   |                 | 公共 IP 地址 | 公共 IP 地址                           | 新建                   |
   |                 |                   | 公共 IP 地址名称                      | CoreServicesVnetGateway-ip   |
   |                 |                   | 公用 IP 地址 SKU                       | 基本                        |
   |                 |                   | 启用主动-主动模式                   | 已禁用                     |
   |                 |                   | 配置 BGP                               | 已禁用                     |
   | 查看 + 创建 |                   | 检查设置，然后选择“**创建**”。 |                              |

   > [!备注] 
   >
   > 最多需要 45 分钟就可创建虚拟网络网关。 

## 任务 7：创建 ManufacturingVnet 网关

1. 在“**搜索资源、服务和文档(G+/)**”中，输入“**虚拟网络网关**”， 然后从结果中选择“**虚拟网络网关**”。

2. 在虚拟网络网关中，选择“**+创建**”。

3. 使用下表中的信息创建虚拟网络网关：

   | **选项卡**         | **部分**       | **选项**                                  | **值**                    |
   | --------------- | ----------------- | ------------------------------------------- | ---------------------------- |
   | 基本          | 项目详细信息   | 订阅                                | 无需任何更改          |
   |                 |                   | ResourceGroup                               | ContosoResourceGroup         |
   |                 | 实例详细信息  | 名称                                        | ManufacturingVnetGateway     |
   |                 |                   | 区域                                      | 西欧                  |
   |                 |                   | 网关类型                                | VPN                          |
   |                 |                   | VPN 类型                                    | 基于路由                  |
   |                 |                   | SKU                                         | VpnGw1                       |
   |                 |                   | 代系                                  | 第 1 代                  |
   |                 |                   | 虚拟网络                             | ManufacturingVnet            |
   |                 |                   | 子网                                      | GatewaySubnet (10.30.0.0/27) |
   |                 | 公共 IP 地址 | 公共 IP 地址                           | 新建                   |
   |                 |                   | 公共 IP 地址名称                      | ManufacturingVnetGateway-ip  |
   |                 |                   | 公用 IP 地址 SKU                       | 基本                        |
   |                 |                   | 启用主动-主动模式                   | 已禁用                     |
   |                 |                   | 配置 BGP                               | 已禁用                     |
   | 查看 + 创建 |                   | 检查设置，然后选择 “**创建**”。 |                              |
   
   > [!备注]
   >
   > 最多需要 45 分钟就可创建虚拟网络网关。 

 

## 任务 8：将 CoreServicesVnet 连接到 ManufacturingVnet 

1. 在“**搜索资源、服务和文档(G+/)**”中，输入“**虚拟网络网关**”，然后从结果中选择“**虚拟网络网关**”。

2. 在虚拟网络网关中，选择 “**CoreServicesVnetGateway**”。

3. 在 CoreServicesGateway 中，选择“**连接**”，然后选择“**+添加**”。

   > [!备注]
   >
   >  直到虚拟网络网关完全部署后，你才能完成此配置。

4. 使用下表中的信息创建连接：

   | **选项**                     | **值**                         |
   | ------------------------------ | --------------------------------- |
   | 名称                           | CoreServicesGW-to-ManufacturingGW |
   | 连接类型                | VNet 到 VNet                      |
   | 第一个虚拟网络网关  | CoreServicesVnetGateway           |
   | 第二个虚拟网络网关 | ManufacturingVnetGateway          |
   | 共享密钥 (PSK)               | abc123                            |
   | 使用 Azure 专用 IP 地址   | 未选择                      |
   | 启用 BGP                     | 未选择                      |
   | IKE 协议                   | IKEv2                             |
   | 订阅                   | 无需任何更改               |
   | 资源组                 | 无需任何更改               |
   | 位置                       | 美国东部                           |

5. 若要创建连接，请选择“**确定**”。
   

## 任务 9：将 ManufacturingVnet 连接到 CoreServicesVnet

1. 在“**搜索资源、服务和文档(G+/)**” 中，输入“**虚拟网络网关**”， 然后从结果中选择“**虚拟网络网关**”。

2. 在虚拟网络网关中，选择“**ManufacturingVnetGateway**”。

3. 在 CoreServicesGateway 中，选择“**连接**”，然后选择“**+添加**”。

4. 使用下表中的信息创建连接：

   | **选项**                     | **值**                         |
   | ------------------------------ | --------------------------------- |
   | 名称                           | ManufacturingGW-to-CoreServicesGW |
   | 连接类型                | VNet 到 VNet                      |
   | 第一个虚拟网络网关  | ManufacturingVnetGateway          |
   | 第二个虚拟网络网关 | CoreServicesVnetGateway           |
   | 共享密钥 (PSK)               | abc123                            |
   | 使用 Azure 专用 IP 地址   | 未选择                      |
   | 启用 BGP                     | 未选择                      |
   | IKE 协议                   | IKEv2                             |
   | 订阅                   | 无需任何更改               |
   | 资源组                 | 无需任何更改               |
   | 位置                       | 西欧                       |

5. 若要创建连接，请选择“**确定**”。

## 任务 10：验证连接是否连接 

1. 在“**搜索资源、服务和文档(G+/)**”中，输入“**连接**”， 然后从结果中选择“**连接**”。

2. 请等到两个连接的状态都为“**已连接**”。 可能需要刷新屏幕。 

   ![已成功创建 VPN 网关连接。](../media/connections-status-connected.png)

 

## 任务 11：测试 VM 之间的连接

1. 在 **ManufacturingTestVM** 上打开 PowerShell。

2. 使用以下命令验证 CoreServicesVnet 上现是否有到 CoreServicesTestVM 的连接。请确保为 CoreServicesTestVM 使用 IPv4 地址。

   ```Powershell
   Test-NetConnection 10.20.20.4 -port 3389
   ```

3. 测试连接应该会成功，你将看到如下结果：

   ![Test-NetConnection 成功。](../media/test-connection-succeeded.png)

 

恭喜！你已使用虚拟网络网关配置了 VNet 到 VNet 连接。
