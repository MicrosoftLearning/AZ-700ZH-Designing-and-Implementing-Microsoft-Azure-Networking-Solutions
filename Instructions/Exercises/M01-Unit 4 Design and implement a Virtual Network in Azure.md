---
Exercise:
    title: '模块 01 第 4 单元 - 在 Azure 中设计和实现虚拟网络'
    module: '模块 - Azure 虚拟网络简介'
---
# 模块 01 第 4 单元 - 在 Azure 中设计和实现虚拟网络


## 练习场景 

现在你已准备好在 Azure 门户中部署虚拟网络。

设想虚构组织 Contoso Ltd，它正在将基础结构和应用程序迁移到 Azure。身为网络工程师，你必须规划和实现三个虚拟网络和子网，以支持这些虚拟网络中的资源。

虚拟网络“**CoreServicesVnet**”部署在“**美国东部**”区域。该虚拟网络将拥有最多的资源。它将通过 VPN 连接与本地网络建立连接。此网络将包含 Web 服务、数据库和其他系统，这些都是业务运营的关键所在。共享服务（如域控制器和 DNS）也将位于此处。预计会有大量增长，因此该虚拟网络需要较大的地址空间。

虚拟网络“**ManufacturingVnet**”部署在靠近你组织的制造工厂位置的“**西欧**”区域。此虚拟网络将包含制造工厂的运营系统。该组织预计会有大量内部连接设备供其系统检索温度等数据，而且还需要一个它可扩展到其中的 IP 地址空间。

虚拟网络“**ResearchVnet**”部署在靠近组织的研发团队位置的“**东南亚**”区域。研发团队使用此虚拟网络。该团队有少量稳定的资源，并且预计这些资源不会增长。团队需要少量的 IP 地址以供少数虚拟机工作。

![Contoso 的网络布局。 
本地 10.10.0.0/16
ResearchVNet 东南亚 10.40.40.0/24
CoreServicesVNet 美国东部 10.20.0.0/16
ManufacturingVNet 西欧 10.30.0.0/16
](../media/design-implement-vnet-peering.png)


你将创建以下资源：
 

| **虚拟网络** | **区域**   | **虚拟网络地址空间** | **子网**                | **子网**    |
| ------------------- | ------------ | --------------------------------- | ------------------------- | ------------- |
| CoreServicesVnet    | 美国东部      | 10.20.0.0/16                      |                           |               |
|                     |              |                                   | GatewaySubnet             | 10.20.0.0/27  |
|                     |              |                                   | SharedServicesSubnet      | 10.20.10.0/24 |
|                     |              |                                   | DatabaseSubnet            | 10.20.20.0/24 |
|                     |              |                                   | PublicWebServiceSubnet    | 10.20.30.0/24 |
| ManufacturingVnet   | 西欧  | 10.30.0.0/16                      |                           |               |
|                     |              |                                   | ManufacturingSystemSubnet | 10.30.10.0/24 |
|                     |              |                                   | SensorSubnet1             | 10.30.20.0/24 |
|                     |              |                                   | SensorSubnet2             | 10.30.21.0/24 |
|                     |              |                                   | SensorSubnet3             | 10.30.22.0/24 |
| ResearchVnet        |东南亚| 10.40.0.0/16                      |                           |               |
|                     |              |                                   | ResearchSystemSubnet      | 10.40.0.0/24  |


这些虚拟网络和子网以一种既可容纳现有资源，又允许预计增长的方式构建。让我们来创建这些虚拟网络和子网，为网络基础结构奠定基础。

在本练习中，你将：

+ 任务 1：创建 Contoso 资源组
+ 任务 2：创建 CoreServicesVnet 虚拟网络和子网
+ 任务 3：创建 ManufacturingVnet 虚拟网络和子网
+ 任务 4：创建 ResearchVnet 虚拟网络和子网
+ 任务 5：验证 VNet 和子网的创建

## 任务 1：创建 Contoso 资源组

1. 转到 [Azure 门户](https://portal.azure.com/)。

2. 在主页的“**Azure 服务**”下，选择“**资源组**”。  

3. 在“资源组”中选择“**+创建**”。

4. 使用下表中的信息创建资源组。

| **选项卡**         | **选项**                                 | **值**            |
| --------------- | ------------------------------------------ | -------------------- |
| 基本          | 资源组                             | ContosoResourceGroup |
|                 | 区域                                     | （美国）美国东部         |
| 标记            | 无需任何更改                        |                      |
| 查看 + 创建 | 检查设置，然后选择 **“创建”** |                      |


5. 在“**资源组**”中，验证 ContosoResourceGroup 是否出现在列表中。

 

## 任务 2：创建 CoreServicesVnet 虚拟网络和子网

1. 在 Azure 门户主页上，导航到全局搜索栏，搜索“**虚拟网络**”并选择服务下的虚拟网络。  
   ![Azure 门户主页上的“全局搜索栏”中的虚拟网络结果。](../media/global-search-bar.PNG)
2. 在“**虚拟网络**”页上选择“创建”。  
   ![创建虚拟网络向导。](../media/create-virtual-network.png)
3. 使用下表中的信息创建 CoreServicesVnet 虚拟网络。  
   ‎删除或覆盖默认 IP 地址空间 
   ![Azure 虚拟网络部署的 IP 地址配置 ](../media/default-vnet-ip-address-range-annotated.png)

 

| **选项卡**      | **选项**         | **值**            |
| ------------ | ------------------ | -------------------- |
| 基本       | 资源组     | ContosoResourceGroup |
|              | 名称               | CoreServicesVnet     |
|              | 区域             | （美国）美国东部         |
| IP 地址 | IPv4 地址空间 | 10.20.0.0/16         |

 4. 使用下表中的信息创建 CoreServicesVnet 子网。

 5. 若要开始创建每个子网，请选择“**+添加子网**”。若要完成创建每个子网，请选择“**添加**”。

| **子网**             | **选项**           | **值**              |
| ---------------------- | -------------------- | ---------------------- |
| GatewaySubnet          | 子网名称          | GatewaySubnet          |
|                        | 子网地址范围 | 10.20.0.0/27           |
| SharedServicesSubnet   | 子网名称          | SharedServicesSubnet   |
|                        | 子网地址范围 | 10.20.10.0/24          |
| DatabaseSubnet         | 子网名称          | DatabaseSubnet         |
|                        | 子网地址范围 | 10.20.20.0/24          |
| PublicWebServiceSubnet | 子网名称          | PublicWebServiceSubnet |
|                        | 子网地址范围 | 10.20.30.0/24          |

 6. 若要完成创建 CoreServicesVnet 及其关联子网，请选择“**查看 + 创建**”。

 7. 验证配置是否通过验证，然后选择“**创建**”。
 
 8. 基于下表对每个 VNet 重复步骤 1-8  

## 任务 3：创建 ManufacturingVnet 虚拟网络和子网


| **选项卡**      | **选项**         | **值**             |
| ------------ | ------------------ | --------------------- |
| 基本       | 资源组     | ContosoResourceGroup  |
|              | 名称               | ManufacturingVnet     |
|              | 区域             | （欧洲）西欧  |
| IP 地址 | IPv4 地址空间 | 10.30.0.0/16          |



| **子网**                | **选项**           | **值**                 |
| ------------------------- | -------------------- | ------------------------- |
| ManufacturingSystemSubnet | 子网名称          | ManufacturingSystemSubnet |
|                           | 子网地址范围 | 10.30.10.0/24             |
| SensorSubnet1             | 子网名称          | SensorSubnet1             |
|                           | 子网地址范围 | 10.30.20.0/24             |
| SensorSubnet2             | 子网名称          | SensorSubnet2             |
|                           | 子网地址范围 | 10.30.21.0/24             |
| SensorSubnet3             | 子网名称          | SensorSubnet3             |
|                           | 子网地址范围 | 10.30.22.0/24             |
 

## 任务 4：创建 ResearchVnet 虚拟网络和子网


| **选项卡**      | **选项**         | **值**            |
| ------------ | ------------------ | -------------------- |
| 基本       | 资源组     | ContosoResourceGroup |
|              | 名称               | ResearchVnet         |
|              | 区域             | 东南亚       |
| IP 地址 | IPv4 地址空间 | 10.40.0.0/16         |

| **子网**           | **选项**           | **值**            |
| -------------------- | -------------------- | -------------------- |
| ResearchSystemSubnet | 子网名称          | ResearchSystemSubnet |
|                      | 子网地址范围 | 10.40.0.0/24         |
 

## 任务 5：验证 VNet 和子网的创建

1. 在 Azure 门户主页上，选择“**所有资源**”。

2. 验证是否列出 CoreServicesVnet、ManufacturingVnet 和 ResearchVnet。

3. 请注意，Azure 为你使用的每个区域创建 NetworkWatcher。

4. 选择“**CoreServicesVnet**”。 

5. 在 CoreServicesVnet 中的“**设置**”下，选择“**子网**”。

6. 在 CoreServicesVnet | 子网中，验证是否列出所创建的子网，以及 IP 地址范围是否正确。

   ![CoreServicesVnet 中的子网列表。](../media/verify-subnets-annotated.png)

7. 为每个 VNet 重复步骤 4-6。

恭喜！你已成功创建一个资源组、三个 VNet 及其关联子网。 
