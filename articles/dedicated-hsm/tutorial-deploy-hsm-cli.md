---
title: Tutorial implementar numa rede virtual existente com a CLI do Azure - HSM dedicada do Azure | Documentos da Microsoft
description: Tutorial que mostra como implementar um HSM dedicados, com a CLI numa rede virtual existente
services: dedicated-hsm
documentationcenter: na
author: barclayn
manager: barbkess
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 6b1b92f25d24294fc5b685b4ce737c03800face3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093097"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Tutorial: Implementar HSMs numa rede virtual existente com a CLI

HSM dedicada do Azure fornece um dispositivo físico para a utilização de cliente único, com o controle administrativo completo e a responsabilidade de gestão completa. A utilização de dispositivos físicos cria a necessidade de Microsoft controlar a alocação de dispositivo para garantir a capacidade é gerenciada com eficiência. Como resultado, uma subscrição do Azure, o serviço HSM dedicado não normalmente serão visível para o aprovisionamento de recursos. Todos os clientes do Azure que necessitam de acesso para o serviço de HSM dedicados, primeiro tem de contactar o executivo da conta Microsoft para o registo de pedido para o serviço de HSM dedicados. Apenas uma vez que este processo seja concluído com êxito o aprovisionamento será possível. 

Este tutorial mostra um processo de aprovisionamento típico em que:

- Um cliente tiver uma rede virtual já
- Eles têm uma máquina virtual
- Precisa de adicionar recursos HSM nesse ambiente existente.

Uma típica, de elevada disponibilidade, arquitetura de implementação em várias regiões pode ter o seguinte aspeto:

![Implementação em várias regiões](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Este tutorial concentra-se num par de HSMs e Gateway do ExpressRoute necessária (veja acima de 1 de sub-rede) que está a ser integrado ao existente virtual de rede (veja acima de 1 de VNET).  Todos os outros recursos são recursos do Azure standard. O mesmo processo de integração pode ser utilizado para HSMs na sub-rede 4 na VNET 3 acima.

## <a name="prerequisites"></a>Pré-requisitos

HSM dedicada do Azure não está atualmente disponível no portal do Azure. Toda a interação com o serviço será através do PowerShell da linha de comandos ou utilizando. Neste tutorial, irá utilizar a interface (CLI) da linha de comandos no Azure Cloud Shell. Se estiver familiarizado com a CLI do Azure, siga instruções de introdução aqui: [Introdução à CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Pressupostos:

- Concluiu o processo de registo de HSM dedicados do Azure
- Foram aprovadas para utilização do serviço. Caso contrário, contacte o seu representante de conta Microsoft para obter detalhes.
- Criou um grupo de recursos para estes recursos e as novas implementadas neste tutorial, irão aderir desse grupo.
- Já criou a rede virtual necessário, sub-rede e máquinas de virtuais de acordo com o diagrama acima e pretende agora integrar 2 HSMs dessa implementação.

Todas as instruções abaixo partem do princípio de que já fizeram navegação no portal do Azure e abrir o Cloud Shell (selecione "\>\_" no canto superior direito do portal).

## <a name="provisioning-a-dedicated-hsm"></a>Aprovisionamento de um HSM dedicados

Aprovisionamento HSMs e integrá-las uma rede virtual existente através do Gateway do ExpressRoute serão validadas utilizando ssh. Esta validação ajuda a garantir a acessibilidade e a disponibilidade básica do dispositivo HSM para qualquer outra as atividades de configuração. Os seguintes comandos irão utilizar um modelo Azure Resource Manager para criar os recursos HSM e os recursos de rede associados.

### <a name="validating-feature-registration"></a>A validar o registo de recurso

Conforme mencionado acima, qualquer atividade de aprovisionamento requer que o serviço de HSM dedicados está registado para a sua subscrição. Para validar que, execute os seguintes comandos na shell do portal de cloud do Azure.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

O seguinte comando verifica os recursos de rede necessários para o serviço de HSM dedicados.

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

Os dois comandos devem retornar um status de "Registada" (conforme mostrado abaixo). Se os comandos não devolverem a "Registado", deve se registrar para este serviço, contacte o seu representante de conta Microsoft.

![Estado da subscrição](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Criar recursos HSM

Um HSM é aprovisionado numa rede virtual dos clientes, para que uma rede virtual e uma sub-rede são necessários. Uma dependência para o HSM permitir a comunicação entre a rede virtual e o dispositivo físico é um Gateway do ExpressRoute e, finalmente, uma máquina virtual é necessária para aceder ao dispositivo HSM com o software de cliente da Gemalto. Esses recursos tenham sido recolhidos num arquivo de modelo, com o ficheiro de parâmetros correspondentes, para facilidade de utilização. Os ficheiros estão disponíveis por contactar a Microsoft diretamente como HSMrequest@Microsoft.com.

Assim que tiver os ficheiros, tem de editar o ficheiro de parâmetros para inserir seus nomes preferenciais dos recursos. Editar linhas com "valor": "".

- `namingInfix` Prefixo de nomes de recursos HSM
- `ExistingVirtualNetworkName` Nome da rede virtual utilizada para os HSMs
- `DedicatedHsmResourceName1` Nome do recurso HSM no carimbo de Data Center 1
- `DedicatedHsmResourceName2` Nome do recurso HSM no carimbo de Data Center 2
- `hsmSubnetRange` Intervalo de endereços IP de sub-rede para HSMs
- `ERSubnetRange` Intervalo de endereços IP de sub-rede para o gateway VNET

Segue-se um exemplo destas alterações:

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

O ficheiro de modelo do Azure Resource Manager associado criará 6 recursos com estas informações:

- Uma sub-rede para os HSMs na VNET especificada
- Uma sub-rede para o gateway de rede virtual
- Um gateway de rede virtual que se liga a VNET para os dispositivos HSM
- Um endereço IP público para o gateway
- Um HSM no carimbo 1
- Um HSM no carimbo de data / 2

Depois de valores de parâmetro são definidos, os arquivos precisam ser carregado para a partilha de ficheiros do shell de cloud de portal do Azure para utilização. No portal do Azure, clique em de "\>\_" cloud shell símbolo canto superior direito e isso fará com que a parte inferior do ecrã de um ambiente de comando. As opções para isso são o BASH e o PowerShell e deve selecionar BASH se não estiver já definido.

A shell de comandos tem uma opção de carregar/transferir na barra de ferramentas e deve selecionar esta opção para carregar os ficheiros de modelo e o parâmetro para a partilha de ficheiros:

![partilha de ficheiros](media/tutorial-deploy-hsm-cli/file-share.png)

Assim que os ficheiros são carregados, está pronto para criar recursos. Antes da criação de HSM novos recursos há alguns pré-requisitos recursos que deve certificar-se estão em vigor. Tem de ter uma rede virtual com intervalos de sub-rede para computação, HSMs e gateway. Os seguintes comandos servem como um exemplo do que seria criar uma rede virtual.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
--vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>A configuração mais importante ter em conta para a rede virtual, é que a sub-rede para o dispositivo HSM tem de ter as delegações definidas como "Microsoft.HardwareSecurityModules/dedicatedHSMs".  O aprovisionamento de HSM não irá funcionar sem esta opção a ser definida.

Depois de todos os pré-requisitos são cumpridos, execute o seguinte comando para utilizar o modelo do Azure Resource Manager, garantindo a atualizar valores com seus nomes exclusivos (pelo menos o nome do grupo de recursos):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Esta implementação deve levar aproximadamente 25 e 30 minutos a ser concluída com a maior parte do tempo a ser os dispositivos HSM

![Estado de aprovisionamento](media/tutorial-deploy-hsm-cli/progress-status.png)

Quando a implementação é concluída com êxito "provisioningState": "Foi concluída com êxito" serão apresentados. Pode ligar à máquina virtual existente e utilizar o SSH para garantir a disponibilidade do dispositivo HSM.

## <a name="verifying-the-deployment"></a>Verificar a implementação

Para verificar se os dispositivos aprovisionados e ver os atributos do dispositivo, execute o seguinte conjunto de comandos. Certifique-se de que o grupo de recursos é definido adequadamente e o nome do recurso é exatamente o que tem no ficheiro de parâmetros.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![saída do aprovisionamento](media/tutorial-deploy-hsm-cli/progress-status2.png)

Agora também será capaz de ver os recursos com o [Explorador de recursos do Azure](https://resources.azure.com/).   Uma vez no explorer, expanda "subscrições" no lado esquerdo, expanda a sua subscrição específica do HSM dedicados, expanda "grupos de recursos", expanda o grupo de recursos que utilizou e por fim, selecione o item "recursos".

## <a name="testing-the-deployment"></a>Teste da implementação

A implementação de teste é um caso de ligação a uma máquina virtual que pode aceder a HSM(s) e, em seguida, ligar remotamente ao dispositivo HSM. Estas ações confirma que o HSM está acessível.
O ssh ferramenta é utilizada para ligar à máquina virtual. O comando seria semelhante ao seguinte, mas com o nome de administrador e o nome dns especificado no parâmetro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

O endereço IP da VM também poderia ser usado em vez do nome DNS no comando acima. Se o comando for bem-sucedida, irá solicitar uma palavra-passe e que deve introduzir que. Uma vez conectado à máquina virtual, pode iniciar sessão para o HSM com o endereço IP privado encontrado no portal para o recurso de interface de rede associado com o HSM.

![lista de componentes](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>A caixa de verificação "Mostrar ocultos tipos", tenha em atenção que, quando selecionado irá apresentar recursos HSM.

A captura de ecrã acima, clicando no "HSM1_HSMnic" ou "HSM2_HSMnic" mostraria o endereço de IP privado adequado. Caso contrário, o `az resource show` comando utilizado acima é uma forma de identificar o endereço IP certo. 

Quando tiver o endereço IP correto, execute o seguinte comando, substituindo esse endereço:

`ssh tenantadmin@10.0.2.4`

Se tiver êxito, será solicitado uma palavra-passe. A palavra-passe predefinida é a palavra-passe e o HSM primeiro solicitará que altere a palavra-passe por isso, defina uma palavra-passe forte e utilizar qualquer mecanismo de sua organização prefere para armazenar a palavra-passe e impedir a perda.

>[!IMPORTANT]
>Se perder a esta palavra-passe, o HSM tem de ser reposto e o que significa perder as suas chaves.

Quando estiver ligado para o HSM utilizar ssh, execute o seguinte comando para garantir que o HSM está operacional.

`hsm show`

O resultado deverá ser conforme mostrado na imagem abaixo:

![lista de componentes](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Neste momento, alocou todos os recursos para uma elevada disponibilidade, dois de HSM de implantação e acesso validado e estado operacional. Qualquer outra configuração ou de teste envolve mais trabalho com o próprio dispositivo HSM. Para isso, deve seguir as instruções no capítulo do guia de administração de 7 rede HSM Gemalto Luna 7 para inicializar o HSM e criar partições. Toda a documentação e software estão disponíveis diretamente a partir da Gemalto para download depois de serem registadas no Portal de suporte do cliente a Gemalto e ter um ID de cliente. Baixe o Software de cliente versão 7.2 para obter todos os componentes necessários.

## <a name="delete-or-clean-up-resources"></a>Eliminar ou limpar os recursos

Se tiver concluído com apenas o dispositivo HSM, em seguida, pode ser eliminada como um recurso e retornado para o pool gratuito. A questão óbvia ao fazer isso é todos os dados confidenciais de clientes que está no dispositivo. Para remover o cliente confidencial dados o dispositivo devem ser com o cliente de Gemalto de reposição de fábrica. Consulte o Guia dos administradores da Gemalto para o dispositivo SafeNet rede Luna 7 e considere os seguintes comandos na ordem.

1. `hsm factoryReset -f`
2. `sysconf config factoryReset -f -service all`
3. `network interface delete -device eth0`
4. `network interface delete -device eth1`
5. `network interface delete -device eth2`
6. `network interface delete -device eth3`
7. `my file clear -f`
8. `my public-key clear -f`
9. `syslog rotate`


> [!NOTE]
> Se tiver um problema com qualquer configuração de dispositivo da Gemalto deve contactar [atendimento ao cliente da Gemalto](https://safenet.gemalto.com/technical-support/).


Se tiver concluído recursos neste grupo de recursos, pode removê-lo tudo com o seguinte comando:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Passos Seguintes

Depois de concluir os passos do tutorial, recursos de HSM dedicados são aprovisionados e tiver uma rede virtual com HSMs necessários e ainda mais os componentes de rede para permitir a comunicação com o HSM.  Agora, está numa posição para complementar esta implementação com mais recursos, conforme exigido pela sua arquitetura de implementação preferenciais. Para obter mais informações em ajudar a planear a implementação, consulte os documentos de conceitos.
Recomenda-se um design com dois HSMs numa região primária endereçamento disponibilidade ao nível do rack e dois HSMs numa região secundária, disponibilidade regional de endereçamento. O ficheiro de modelo utilizado neste tutorial pode ser facilmente utilizado como base para uma implementação de HSM dois, mas tem de ter seus parâmetros modificados para satisfazer os seus requisitos.

* [Elevada disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitorização](monitoring.md)
