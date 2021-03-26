---
title: Tutorial implantado numa rede virtual existente utilizando o PowerShell - Azure Dedicado HSM | Microsoft Docs
description: Tutorial mostrando como implementar um HSM dedicado usando o PowerShell numa rede virtual existente
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 5ed5ac90f446f74c54488f6d0cf23adbd63a3e1e
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606883"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Tutorial – Implantação de HSMs numa rede virtual existente utilizando o PowerShell

O Azure Dedicad HSM Service fornece um dispositivo físico para uso exclusivo do cliente, com controlo administrativo completo e total responsabilidade de gestão. Devido ao fornecimento de hardware físico, a Microsoft deve controlar a forma como estes dispositivos são atribuídos para garantir que a capacidade é gerida de forma eficaz. Como resultado, dentro de uma subscrição Azure, o serviço dedicado HSM não será normalmente visível para o fornecimento de recursos. Qualquer cliente Azure que necessite de acesso ao serviço dedicado HSM deve primeiro contactar o seu executivo de conta Microsoft para solicitar o registo do serviço Dedicado HSM. Só uma vez concluído este processo com sucesso será possível o provisionamento.
Este tutorial tem como objetivo mostrar um processo típico de provisionamento onde:

- Um cliente já tem uma rede virtual
- Eles têm uma máquina virtual
- Precisam de adicionar recursos HSM a esse ambiente existente.

Uma arquitetura típica, de alta disponibilidade, multi-região de implantação pode parecer a seguinte:

![implantação multi-região](media/tutorial-deploy-hsm-powershell/high-availability.png)

Este tutorial centra-se num par de HSMs e no ExpressRoute Gateway necessário (ver Subnet 1 acima) sendo integrado numa rede virtual existente (ver VNET 1 acima).  Todos os outros recursos são recursos Azure padrão. O mesmo processo de integração pode ser utilizado para HSMs na sub-rede 4 em VNET 3 acima.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

O Azure Dedicated HSM não está atualmente disponível no portal Azure, pelo que toda a interação com o serviço será via linha de comando ou utilizando o PowerShell. Este tutorial usará PowerShell na Azure Cloud Shell. Se é novo na PowerShell, siga as instruções de início aqui: [Azure PowerShell Get Start](/powershell/azure/get-started-azureps).

Pressupostos:

- Já passou pelo processo de registo do HSM dedicado à Azure e foi aprovado para utilização do serviço. Caso contrário, contacte o representante da sua conta microsoft para obter mais detalhes. 
- Criou um Grupo de Recursos para estes recursos e os novos implantados neste tutorial juntar-se-ão a esse grupo.
- Já criou a rede virtual necessária, sub-rede e máquinas virtuais de acordo com o diagrama acima e agora quer integrar 2 HSMs nessa implementação.

Todas as instruções abaixo assumem que já navegaram até ao portal Azure e que abriu a Cloud Shell (selecione \> \_ " " para o topo direito do portal).

## <a name="provisioning-a-dedicated-hsm"></a>Provisionamento de um HSM dedicado

O fornecimento dos HSMs e a integração numa rede virtual existente através do ExpressRoute Gateway serão validados utilizando a ferramenta de linha de comando ssh para garantir a alcance e a disponibilidade básica do dispositivo HSM para quaisquer atividades de configuração posteriores. Os seguintes comandos utilizarão um modelo de Gestor de Recursos para criar os recursos HSM e os recursos de networking associados.

### <a name="validating-feature-registration"></a>Validação do Registo de Recursos

Como mencionado acima, qualquer atividade de provisionamento requer que o serviço dedicado HSM esteja registado para a sua subscrição. Para validar isso, executar o seguinte comando PowerShell na casca de nuvem do portal Azure. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

O comando deve devolver um estado de "Registrado" (como mostrado abaixo) antes de prosseguir.  Se não estiver registado neste serviço contacte o representante da sua conta microsoft.

![estado de subscrição](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>Criação de recursos HSM

Um dispositivo HSM é a provisionado numa rede virtual de clientes. Isto implica a exigência de uma sub-rede. Uma dependência do HSM para permitir a comunicação entre a rede virtual e o dispositivo físico é um Gateway ExpressRoute, e finalmente uma máquina virtual é necessária para aceder ao dispositivo HSM usando o software do cliente Thales. Estes recursos foram recolhidos num ficheiro de modelo, com o respetivo arquivo de parâmetros, para facilitar a utilização. Os ficheiros estão disponíveis contactando a Microsoft diretamente em HSMrequest@Microsoft.com .

Uma vez que tenha os ficheiros, tem de editar o ficheiro de parâmetros para inserir os seus nomes preferidos para obter recursos. Isto significa editar linhas com "valor": "".

- `namingInfix` Prefixo para nomes de recursos HSM
- `ExistingVirtualNetworkName` Nome da rede virtual utilizada para os HSMs
- `DedicatedHsmResourceName1` Nome do recurso HSM no carimbo do datacenter 1
- `DedicatedHsmResourceName2` Nome do recurso HSM no carimbo do datacenter 2
- `hsmSubnetRange` Gama de endereços IP sub-rede para HSMs
- `ERSubnetRange` Gama de endereços IP sub-rede para gateway VNET

Um exemplo destas alterações é o seguinte:

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

O ficheiro de modelo associado do Gestor de Recursos criará 6 recursos com esta informação:

- Uma sub-rede para os HSMs no VNET especificado
- Uma sub-rede para o gateway de rede virtual 
- Um gateway de rede virtual que liga o VNET aos dispositivos HSM
- Um endereço IP público para o portal
- Um HSM no selo 1
- Um HSM no carimbo 2

Uma vez definidos os valores dos parâmetros, os ficheiros precisam de ser enviados para a partilha de ficheiros de shell do portal Azure para utilização. No portal Azure, clique no símbolo da concha da \> \_ nuvem " " no topo direito e isto fará da parte inferior do ecrã um ambiente de comando. As opções para isso são BASH e PowerShell e deve selecionar BASH se ainda não estiver definido.

O invólucro de comando tem uma opção de upload/download na barra de ferramentas e deve selecioná-lo para carregar os ficheiros do modelo e dos parâmetros para a sua partilha de ficheiros:

![partilha de arquivos](media/tutorial-deploy-hsm-powershell/file-share.png)

Assim que os ficheiros forem carregados, está pronto para criar recursos.
Antes de criar novos recursos HSM, existem alguns recursos pré-necessários que deve garantir que estão em vigor. Deve ter uma rede virtual com gamas de sub-redes para computação, HSMs e gateway. Os seguintes comandos servem como um exemplo do que criaria uma rede virtual.

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
>A configuração mais importante a notar para a rede virtual, é que a sub-rede para o dispositivo HSM deve ter delegações definidas para "Microsoft.HardwareSecurityModules/dedicadohSMs".  O fornecimento do HSM não funcionará sem isto.

Uma vez que todos os pré-requisitos estejam em vigor, executar o seguinte comando para usar o modelo de Gestor de Recursos garantindo que tem valores atualizados com os seus nomes únicos (pelo menos o nome do grupo de recursos):

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

Este comando deve levar aproximadamente 20 minutos para ser concluído. A opção "verbose" utilizada garantirá que o estado é continuamente visualizado.

![estatuto de provisionamento](media/tutorial-deploy-hsm-powershell/progress-status.png)

Quando concluído com sucesso, mostrado por "ProvisioningState": "Bem sucedido", pode iniciar sôming na sua máquina virtual existente e utilizar o SSH para garantir a disponibilidade do dispositivo HSM.

## <a name="verifying-the-deployment"></a>Verificação da implantação

Para verificar se os dispositivos foram a provisionados e ver os atributos do dispositivo, execute o seguinte conjunto de comandos. Certifique-se de que o grupo de recursos está definido adequadamente e que o nome do recurso é exatamente como tem no ficheiro do parâmetro.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![estatuto de prestação](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Agora também poderá ver os recursos utilizando o [explorador de recursos Azure.](https://resources.azure.com/)   Uma vez no explorador, expandir as "subscrições" à esquerda, expandir a sua subscrição específica para O HSM dedicado, expandir "grupos de recursos", expandir o grupo de recursos que utilizou e finalmente selecionar o item "recursos".

## <a name="testing-the-deployment"></a>Testar a Implementação

Testar a implementação é um caso de ligação a uma máquina virtual que pode aceder ao(s) HSM(s) e, em seguida, ligar-se diretamente ao dispositivo HSM. Estas ações confirmarão que o HSM está acessível.
A ferramenta ssh é utilizada para ligar à máquina virtual. O comando será semelhante ao seguinte, mas com o nome do administrador e o nome DNS especificado no parâmetro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A palavra-passe a usar é a do ficheiro de parâmetros.
Uma vez iniciado o login no VM Linux, pode iniciar sessão no HSM utilizando o endereço IP privado encontrado no portal para o recurso \<prefix> hsm_vnic.

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Quando tiver o endereço IP, execute o seguinte comando:

`ssh tenantadmin@<ip address of HSM>`

Se for bem sucedido, será solicitado uma senha. A palavra-passe predefinida é PASSWORD. O HSM pedir-lhe-á para alterar a sua palavra-passe para definir uma palavra-passe forte e utilizar qualquer mecanismo que a sua organização prefira armazenar a palavra-passe e evitar a perda.  

>[!IMPORTANT]
>se perder esta palavra-passe, o HSM terá de ser reiniciado e isso significa perder as chaves.

Quando estiver ligado ao dispositivo HSM utilizando o ssh, executar o seguinte comando para garantir que o HSM está operacional.

`hsm show`

A saída deve parecer-se com a imagem mostrada abaixo:

![Screenshot que mostra a saída do comando do programa hsm.](media/tutorial-deploy-hsm-powershell/output.png)

Neste momento, alocou todos os recursos para uma implantação altamente disponível, duas implantações de HSM e acesso validado e estado operacional. Qualquer outra configuração ou teste envolve mais trabalho com o próprio dispositivo HSM. Para isso, deve seguir as instruções no Guia de Administração Thales Luna 7 HSM capítulo 7 para rubricar o HSM e criar divisórias. Toda a documentação e software estão disponíveis diretamente do Thales para download uma vez registado no [portal de apoio ao cliente thales](https://supportportal.thalesgroup.com/csm) e tem um ID do Cliente. Baixe a versão 7.2 do Software cliente para obter todos os componentes necessários.

## <a name="delete-or-clean-up-resources"></a>Eliminar ou limpar recursos

Se terminou apenas com o dispositivo HSM, então pode ser eliminado como recurso e devolvido à piscina gratuita. A preocupação óbvia ao fazê-lo são quaisquer dados sensíveis do cliente que estão no dispositivo. A melhor maneira de "zeroizar" um dispositivo é obter a palavra-passe de administração HSM errada 3 vezes (nota: isto não é administração de aparelho, é o administrador HSM real). Como medida de segurança para proteger o material chave, o dispositivo não pode ser eliminado como recurso Azure até estar no estado zeroizado.

> [!NOTE]
> se tiver problemas com qualquer configuração do dispositivo Thales, deve contactar o [suporte ao cliente thales](https://supportportal.thalesgroup.com/csm).

Se pretender remover o recurso HSM em Azure, pode utilizar o seguinte comando substituindo as variáveis "$" pelos seus parâmetros únicos:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Passos seguintes

Após completar os passos no tutorial, os recursos dedicados do HSM são a provisionados e disponíveis na sua rede virtual. Está agora em posição de felicitar esta implantação com mais recursos, conforme exigido pela sua arquitetura de implantação preferida. Para obter mais informações sobre como ajudar a planear a sua implantação, consulte os documentos do Concepts. Recomenda-se um desenho com dois HSMs numa região primária que aborde a disponibilidade ao nível da cremalheira, e dois HSMs numa região secundária que aborde a disponibilidade regional. O ficheiro de modelo utilizado neste tutorial pode ser facilmente usado como base para uma implantação de dois HSM, mas precisa de ter os seus parâmetros modificados para satisfazer os seus requisitos.

* [Alta Disponibilidade](high-availability.md)
* [Segurança Física](physical-security.md)
* [Rede](networking.md)
* [Monitorização](monitoring.md)
* [Suportabilidade](supportability.md)
