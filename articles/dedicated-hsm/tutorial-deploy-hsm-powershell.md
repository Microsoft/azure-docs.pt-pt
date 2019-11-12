---
title: Tutorial implantar em uma rede virtual existente usando o PowerShell – HSM dedicado do Azure | Microsoft Docs
description: Tutorial mostrando como implantar um HSM dedicado usando o PowerShell em uma rede virtual existente
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 2f605d5adda913fa465b43a85bd027458959c122
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928099"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Tutorial – implantando HSMs em uma rede virtual existente usando o PowerShell

O serviço HSM dedicado do Azure fornece um dispositivo físico para uso único do cliente, com controle administrativo completo e responsabilidade de gerenciamento total. Devido ao fornecimento de hardware físico, a Microsoft deve controlar como esses dispositivos são alocados para garantir que a capacidade seja gerenciada com eficiência. Como resultado, em uma assinatura do Azure, o serviço HSM dedicado normalmente não estará visível para o provisionamento de recursos. Qualquer cliente do Azure que precise de acesso ao serviço HSM dedicado deve primeiro entrar em contato com seu executivo de conta Microsoft para solicitar o registro do serviço HSM dedicado. Somente quando esse processo for concluído com êxito, o provisionamento será possível.
Este tutorial visa mostrar um processo de provisionamento típico em que:

- Um cliente já tem uma rede virtual
- Eles têm uma máquina virtual
- Eles precisam adicionar recursos de HSM a esse ambiente existente.

Uma arquitetura de implantação típica, de alta disponibilidade e de várias regiões pode ter a seguinte aparência:

![implantação em várias regiões](media/tutorial-deploy-hsm-powershell/high-availability.png)

Este tutorial concentra-se em um par de HSMs e o gateway de ExpressRoute necessário (consulte a sub-rede 1 acima) que está sendo integrado a uma rede virtual existente (consulte VNET 1 acima).  Todos os outros recursos são recursos padrão do Azure. O mesmo processo de integração pode ser usado para HSMs na sub-rede 4 na VNET 3 acima.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

O HSM dedicado do Azure não está disponível no momento no portal do Azure, portanto, toda a interação com o serviço será via linha de comando ou usando o PowerShell. Este tutorial usará o PowerShell no Azure Cloud Shell. Se você for novo no PowerShell, siga as instruções de introdução aqui: [Azure PowerShell começar](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Suposições

- Você passou pelo processo de registro do HSM dedicado do Azure e foi aprovado para uso do serviço. Caso contrário, entre em contato com seu representante de conta Microsoft para obter detalhes. 
- Você criou um grupo de recursos para esses recursos e os novos implantados neste tutorial ingressarão nesse grupo.
- Você já criou a rede virtual, a sub-rede e as máquinas virtuais necessárias, de acordo com o diagrama acima, e agora deseja integrar 2 HSMs a essa implantação.

Todas as instruções abaixo pressupõem que você já navegou até a portal do Azure e abriu a Cloud Shell (selecione "\>\_" na parte superior direita do Portal).

## <a name="provisioning-a-dedicated-hsm"></a>Provisionando um HSM dedicado

O provisionamento dos HSMs e a integração em uma rede virtual existente por meio do gateway de ExpressRoute será validado usando a ferramenta de linha de comando ssh para garantir a acessibilidade e a disponibilidade básica do dispositivo HSM para qualquer outra atividade de configuração. Os comandos a seguir usarão um modelo do Resource Manager para criar os recursos de HSM e os recursos de rede associados.

### <a name="validating-feature-registration"></a>Validando o registro de recursos

Conforme mencionado acima, qualquer atividade de provisionamento requer que o serviço HSM dedicado esteja registrado para sua assinatura. Para validar isso, execute o seguinte comando do PowerShell no portal do Azure cloud Shell. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

O comando a seguir verifica os recursos de rede necessários para o serviço HSM dedicado.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowBaremetalServers
```

Os dois comandos devem retornar um status "registrado" (como mostrado abaixo) antes de prosseguir.  Se você precisar se registrar para esse serviço, entre em contato com seu representante de conta Microsoft.

![status da assinatura](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>Criando recursos HSM

Um dispositivo HSM é provisionado na rede virtual de um cliente. Isso implica o requisito de uma sub-rede. Uma dependência para o HSM habilitar a comunicação entre a rede virtual e o dispositivo físico é um gateway de ExpressRoute e, finalmente, uma máquina virtual é necessária para acessar o dispositivo HSM usando o software cliente do Gemalto. Esses recursos foram coletados em um arquivo de modelo, com o arquivo de parâmetro correspondente, para facilitar o uso. Os arquivos estão disponíveis entrando em contato com a Microsoft diretamente em HSMrequest@Microsoft.com.

Depois de ter os arquivos, você deve editar o arquivo de parâmetro para inserir seus nomes preferenciais para os recursos. Isso significa Editar linhas com "valor": "".

- `namingInfix` prefixo para os nomes de recursos HSM
- `ExistingVirtualNetworkName` nome da rede virtual usada para os HSMs
- `DedicatedHsmResourceName1` nome do recurso HSM no carimbo de Data Center 1
- `DedicatedHsmResourceName2` nome do recurso HSM no carimbo de Data Center 2
- intervalo de endereços IP de sub-rede `hsmSubnetRange` para HSMs
- intervalo de endereços IP de sub-rede `ERSubnetRange` para o gateway de VNET

Um exemplo dessas alterações é o seguinte:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

O arquivo de modelo do Resource Manager associado criará 6 recursos com essas informações:

- Uma sub-rede para os HSMs na VNET especificada
- Uma sub-rede para o gateway de rede virtual 
- Um gateway de rede virtual que conecta a VNET aos dispositivos HSM
- Um endereço IP público para o gateway
- Um HSM no Stamp 1
- Um HSM no Stamp 2

Depois que os valores de parâmetro são definidos, os arquivos precisam ser carregados para portal do Azure compartilhamento de arquivos do Cloud Shell para uso. Na portal do Azure, clique no símbolo do Shell de nuvem "\>\_" na parte superior direita e isso fará com que a parte inferior da tela seja um ambiente de comando. As opções para isso são BASH e PowerShell, e você deve selecionar BASH se ainda não estiver definido.

O Shell de comando tem uma opção de upload/download na barra de ferramentas e você deve selecionar para carregar os arquivos de modelo e parâmetro em seu compartilhamento de arquivos:

![compartilhamento de arquivos](media/tutorial-deploy-hsm-powershell/file-share.png)

Depois que os arquivos forem carregados, você estará pronto para criar recursos.
Antes de criar novos recursos do HSM, há alguns recursos de pré-requisito que você deve garantir que estejam em vigor. Você deve ter uma rede virtual com intervalos de sub-rede para computação, HSMs e gateway. Os comandos a seguir servem como um exemplo do que criaria uma rede virtual desse tipo.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>A configuração mais importante a ser observada para a rede virtual é que a sub-rede para o dispositivo HSM deve ter delegações definidas como "Microsoft. HardwareSecurityModules/dedicatedHSMs".  O provisionamento do HSM não funcionará sem isso.

Depois que todos os pré-requisitos estiverem em vigor, execute o seguinte comando para usar o modelo do Resource Manager, garantindo que você tenha atualizado valores com seus nomes exclusivos (pelo menos o nome do grupo de recursos):

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Esse comando deve levar aproximadamente 20 minutos para ser concluído. A opção "-Verbose" usada garantirá que o status seja exibido continuamente.

![Status de provisionamento](media/tutorial-deploy-hsm-powershell/progress-status.png)

Quando concluído com êxito, mostrado por "provisioningState": "êxito", você pode entrar em sua máquina virtual existente e usar o SSH para garantir a disponibilidade do dispositivo HSM.

## <a name="verifying-the-deployment"></a>Verificando a implantação

Para verificar se os dispositivos foram provisionados e ver atributos de dispositivo, execute o seguinte conjunto de comandos. Verifique se o grupo de recursos está definido corretamente e se o nome do recurso é exatamente o que você tem no arquivo de parâmetro.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![status de provisionamento](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Agora, você também poderá ver os recursos usando o Gerenciador de [recursos do Azure](https://resources.azure.com/).   Uma vez no Explorer, expanda "assinaturas" à esquerda, expanda sua assinatura específica para HSM dedicado, expanda "grupos de recursos", expanda o grupo de recursos que você usou e, por fim, selecione o item "recursos".

## <a name="testing-the-deployment"></a>Testando a implantação

Testar a implantação é um caso de conexão a uma máquina virtual que pode acessar o HSM (s) e, em seguida, conectar-se diretamente ao dispositivo HSM. Essas ações confirmarão que o HSM está acessível.
A ferramenta SSH é usada para se conectar à máquina virtual. O comando será semelhante ao seguinte, mas com o nome do administrador e o nome DNS que você especificou no parâmetro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A senha a ser usada é a do arquivo de parâmetro.
Depois de conectado à VM Linux, você pode fazer logon no HSM usando o endereço IP privado encontrado no portal para o prefixo de \<de recurso > hsm_vnic.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Quando você tiver o endereço IP, execute o seguinte comando:

`ssh tenantadmin@<ip address of HSM>`

Se for bem-sucedido, você será solicitado a fornecer uma senha. A senha padrão é PASSWORD. O HSM solicitará que você altere sua senha, portanto, defina uma senha forte e use qualquer mecanismo que sua organização prefira para armazenar a senha e evitar a perda.  

>[!IMPORTANT]
>Se você perder essa senha, o HSM precisará ser redefinido e isso significa perder suas chaves.

Quando você estiver conectado ao dispositivo HSM usando o ssh, execute o comando a seguir para garantir que o HSM esteja operacional.

`hsm show`

A saída deve ser parecida com a imagem mostrada abaixo:

![status de provisionamento](media/tutorial-deploy-hsm-powershell/output.png)

Neste ponto, você alocou todos os recursos para uma implantação do HSM altamente disponível, duas implantações e um estado operacional Qualquer configuração ou teste adicional envolve mais trabalho com o próprio dispositivo HSM. Para isso, você deve seguir as instruções no guia de administração do HSM 7 de rede Gemalto Luna, capítulo 7 para inicializar o HSM e criar partições. Toda a documentação e o software estão disponíveis diretamente do Gemalto para download depois que você estiver registrado no portal de suporte ao cliente do Gemalto e tiver uma ID do cliente. Baixe o software cliente versão 7,2 para obter todos os componentes necessários.

## <a name="delete-or-clean-up-resources"></a>Excluir ou limpar recursos

Se você tiver concluído apenas o dispositivo HSM, ele poderá ser excluído como um recurso e retornado para o pool gratuito. A preocupação óbvia ao fazer isso é qualquer dado de cliente confidencial que está no dispositivo. Para remover dados confidenciais do cliente, o dispositivo deve ser redefinido de fábrica usando o cliente Gemalto. Consulte o guia de administradores do Gemalto para o dispositivo de rede SafeNet Luna 7 e considere os seguintes comandos na ordem.

1. `hsm factoryReset -f`
2. `sysconf config factoryReset -f -service all`
3. `my file clear -f`
4. `my public-key clear -f`
5. `syslog rotate`


> [!NOTE]
> Se você tiver problemas com qualquer configuração de dispositivo Gemalto, entre em contato com o [suporte ao cliente do Gemalto](https://safenet.gemalto.com/technical-support/).

Se tiver concluído os recursos nesse grupo de recursos, você poderá removê-los com o seguinte comando:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Passos seguintes

Depois de concluir as etapas no tutorial, os recursos de HSM dedicados são provisionados e disponibilizados em sua rede virtual. Agora você está em uma posição para complementar essa implantação com mais recursos, conforme exigido pela sua arquitetura de implantação preferida. Para obter mais informações sobre como ajudar a planejar sua implantação, consulte os documentos de conceitos. Um design com dois HSMs em uma região primária que aborda a disponibilidade no nível do rack e dois HSMs em uma região secundária que aborda a disponibilidade regional é recomendado. O arquivo de modelo usado neste tutorial pode ser facilmente usado como base para uma implantação de dois HSM, mas precisa ter seus parâmetros modificados para atender às suas necessidades.

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Monitorização](monitoring.md)
* [Capacidade](supportability.md)
