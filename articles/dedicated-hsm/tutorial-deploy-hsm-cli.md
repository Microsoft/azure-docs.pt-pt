---
title: Tutorial implantado numa rede virtual existente utilizando o Azure CLI - Azure Dedicado HSM ; Microsoft Docs
description: Tutorial mostrando como implementar um HSM dedicado usando o CLI em uma rede virtual existente
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 63cdb27663cb1a2d8de1a97a2f352b05ff57a3f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89489889"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Tutorial: Implantação de HSMs numa rede virtual existente utilizando o CLI

A Azure Dedicated HSM fornece um dispositivo físico para uso exclusivo do cliente, com controlo administrativo completo e total responsabilidade de gestão. A utilização de dispositivos físicos cria a necessidade de a Microsoft controlar a alocação de dispositivos para garantir que a capacidade é gerida de forma eficaz. Como resultado, dentro de uma subscrição Azure, o serviço dedicado HSM não será normalmente visível para o fornecimento de recursos. Qualquer cliente Azure que necessite de acesso ao serviço dedicado HSM deve primeiro contactar o seu executivo de conta Microsoft para solicitar o registo do serviço Dedicado HSM. Só uma vez concluído este processo com sucesso será possível o provisionamento. 

Este tutorial mostra um processo típico de provisionamento onde:

- Um cliente já tem uma rede virtual
- Eles têm uma máquina virtual
- Precisam de adicionar recursos HSM a esse ambiente existente.

Uma arquitetura típica, de alta disponibilidade, multi-região de implantação pode parecer a seguinte:

![implantação multi-região](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Este tutorial centra-se num par de HSMs e o ExpressRoute Gateway necessário (ver Subnet 1 acima) sendo integrado numa rede virtual existente (ver VNET 1 acima).  Todos os outros recursos são recursos Azure padrão. O mesmo processo de integração pode ser utilizado para HSMs na sub-rede 4 em VNET 3 acima.

## <a name="prerequisites"></a>Pré-requisitos

A Azure Dedicated HSM não está atualmente disponível no portal Azure. Toda a interação com o serviço será através de uma linha de comando ou utilizando o PowerShell. Este tutorial utilizará a interface de linha de comando (CLI) na Azure Cloud Shell. Se é novo no Azure CLI, siga as instruções de início aqui: [Azure CLI 2.0 Get Start](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Pressupostos:

- Completou o processo de registo dedicado ao HSM Azure
- Foi aprovado para uso do serviço. Caso contrário, contacte o representante da sua conta microsoft para obter mais detalhes.
- Criou um Grupo de Recursos para estes recursos e os novos implantados neste tutorial juntar-se-ão a esse grupo.
- Já criou a rede virtual necessária, sub-rede e máquinas virtuais de acordo com o diagrama acima e agora quer integrar 2 HSMs nessa implementação.

Todas as instruções abaixo assumem que já navegaram até ao portal Azure e que abriu a Cloud Shell (selecione \> \_ " " para o topo direito do portal).

## <a name="provisioning-a-dedicated-hsm"></a>Provisionamento de um HSM dedicado

O fornecimento de HSMs e a sua integração numa rede virtual existente através do ExpressRoute Gateway serão validados através do ssh. Esta validação ajuda a garantir a alcance e a disponibilidade básica do dispositivo HSM para quaisquer outras atividades de configuração. Os seguintes comandos utilizarão um modelo de Gestor de Recursos Azure para criar os recursos HSM e recursos de rede associados.

### <a name="validating-feature-registration"></a>Validação do Registo de Recursos

Como mencionado acima, qualquer atividade de provisionamento requer que o serviço dedicado HSM esteja registado para a sua subscrição. Para validar isso, executar os seguintes comandos na casca de nuvem do portal Azure.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

Os comandos devem devolver um estado de "Registado" (como mostrado abaixo). Se os comandos não devolverem "Registado" tem de se registar para este serviço contactando o seu representante da conta Microsoft.

![estado de subscrição](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Criação de recursos HSM

Um HSM é alotado na rede virtual de um cliente para que seja necessária uma rede virtual e uma sub-rede. Uma dependência do HSM para permitir a comunicação entre a rede virtual e o dispositivo físico é um Gateway ExpressRoute, e finalmente uma máquina virtual é necessária para aceder ao dispositivo HSM usando o software do cliente Gemalto. Estes recursos foram recolhidos num ficheiro de modelo, com o respetivo arquivo de parâmetros, para facilitar a utilização. Os ficheiros estão disponíveis contactando a Microsoft diretamente como HSMrequest@Microsoft.com .

Uma vez que tenha os ficheiros, tem de editar o ficheiro de parâmetros para inserir os seus nomes preferidos para obter recursos. Editar linhas com "valor": "".

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

O ficheiro de modelo associado do Gestor de Recursos Azure criará 6 recursos com esta informação:

- Uma sub-rede para os HSMs no VNET especificado
- Uma sub-rede para o gateway de rede virtual
- Um gateway de rede virtual que liga o VNET aos dispositivos HSM
- Um endereço IP público para o portal
- Um HSM no selo 1
- Um HSM no carimbo 2

Uma vez definidos os valores dos parâmetros, os ficheiros precisam de ser enviados para a partilha de ficheiros de shell do portal Azure para utilização. No portal Azure, clique no símbolo da concha da \> \_ nuvem " " no topo direito e isto fará da parte inferior do ecrã um ambiente de comando. As opções para isso são BASH e PowerShell e deve selecionar BASH se ainda não estiver definido.

O invólucro de comando tem uma opção de upload/download na barra de ferramentas e deve selecioná-lo para carregar os ficheiros do modelo e dos parâmetros para a sua partilha de ficheiros:

![partilha de arquivos](media/tutorial-deploy-hsm-cli/file-share.png)

Assim que os ficheiros forem carregados, está pronto para criar recursos. Antes de criar novos recursos HSM, existem alguns recursos pré-necessários que deve garantir que estão em vigor. Deve ter uma rede virtual com gamas de sub-redes para computação, HSMs e gateway. Os seguintes comandos servem como um exemplo do que criaria uma rede virtual.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
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
>A configuração mais importante a notar para a rede virtual, é que a sub-rede para o dispositivo HSM deve ter delegações definidas para "Microsoft.HardwareSecurityModules/dedicadohSMs".  O provisionamento do HSM não funcionará sem que esta opção seja definida.

Uma vez que todos os pré-requisitos estejam em vigor, executar o seguinte comando para usar o modelo Azure Resource Manager garantindo que tem valores atualizados com os seus nomes únicos (pelo menos o nome do grupo de recursos):

```azurecli
az group deployment create \
   --resource-group myRG  \
   --template-file ./Deploy-2HSM-toVNET-Template.json \
   --parameters ./Deploy-2HSM-toVNET-Params.json \
   --name HSMdeploy \
   --verbose
```

Esta implementação deve demorar aproximadamente 25 a 30 minutos para ser completada com a maior parte desse tempo sendo os dispositivos HSM

![estatuto de provisionamento](media/tutorial-deploy-hsm-cli/progress-status.png)

Quando a implementação terminar com sucesso "ProvisioningState": "Bem-sucedido" será exibido. Pode ligar-se à sua máquina virtual existente e utilizar o SSH para garantir a disponibilidade do dispositivo HSM.

## <a name="verifying-the-deployment"></a>Verificação da implantação

Para verificar se os dispositivos foram a provisionados e ver os atributos do dispositivo, execute o seguinte conjunto de comandos. Certifique-se de que o grupo de recursos está definido adequadamente e que o nome do recurso é exatamente como tem no ficheiro do parâmetro.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![provisão de produção](media/tutorial-deploy-hsm-cli/progress-status2.png)

Agora também poderá ver os recursos utilizando o [explorador de recursos Azure.](https://resources.azure.com/)   Uma vez no explorador, expandir as "subscrições" à esquerda, expandir a sua subscrição específica para O HSM dedicado, expandir "grupos de recursos", expandir o grupo de recursos que utilizou e finalmente selecionar o item "recursos".

## <a name="testing-the-deployment"></a>Testar a Implementação

Testar a implementação é um caso de ligação a uma máquina virtual que pode aceder ao(s) HSM(s) e, em seguida, ligar-se diretamente ao dispositivo HSM. Estas ações confirmarão que o HSM está acessível.
A ferramenta ssh é utilizada para ligar à máquina virtual. O comando será semelhante ao seguinte, mas com o nome do administrador e o nome DNS especificado no parâmetro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

O endereço IP do VM também poderia ser utilizado no lugar do nome DNS no comando acima. Se o comando for bem sucedido, irá solicitar uma senha e deverá inseri-la. Uma vez iniciado o login na máquina virtual, pode iniciar sessão no HSM utilizando o endereço IP privado encontrado no portal para o recurso de interface de rede associado ao HSM.

![lista de componentes](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Note a caixa de verificação "Mostrar tipos ocultos", que quando selecionada apresentará os recursos HSM.

Na imagem acima, clicar no "HSM1_HSMnic" ou "HSM2_HSMnic" mostraria o endereço IP privado apropriado. Caso contrário, o `az resource show` comando acima utilizado é uma forma de identificar o Endereço IP certo. 

Quando tiver o endereço IP correto, execute o seguinte comando substituindo esse endereço:

`ssh tenantadmin@10.0.2.4`

Se for bem sucedido, será solicitado uma senha. A palavra-passe padrão é PASSWORD e o HSM pedir-lhe-á primeiro que altere a sua palavra-passe para definir uma palavra-passe forte e utilizar qualquer mecanismo que a sua organização prefira armazenar a palavra-passe e evitar a perda.

>[!IMPORTANT]
>se perder esta palavra-passe, o HSM terá de ser reiniciado e isso significa perder as chaves.

Quando estiver ligado ao HSM utilizando o ssh, executar o seguinte comando para garantir que o HSM está operacional.

`hsm show`

A saída deve ser vista como mostrado na imagem abaixo:

![lista de componentes](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Neste momento, alocou todos os recursos para uma implantação altamente disponível, duas implantações de HSM e acesso validado e estado operacional. Qualquer outra configuração ou teste envolve mais trabalho com o próprio dispositivo HSM. Para isso, deve seguir as instruções no Guia de Administração da Rede Gemalto Luna HSM 7 capítulo 7 para rubricar o HSM e criar divisórias. Toda a documentação e software estão disponíveis diretamente de Gemalto para download uma vez registado no Portal de Apoio ao Cliente gemalto e tem um ID do Cliente. Baixe a versão 7.2 do Software cliente para obter todos os componentes necessários.

## <a name="delete-or-clean-up-resources"></a>Eliminar ou limpar recursos

Se terminou apenas com o dispositivo HSM, então pode ser eliminado como recurso e devolvido à piscina gratuita. A preocupação óbvia ao fazê-lo são quaisquer dados sensíveis do cliente que estão no dispositivo. A melhor maneira de "zeroizar" um dispositivo é obter a palavra-passe de administração HSM errada 3 vezes (nota: isto não é administração de aparelho, é o administrador HSM real). Como medida de segurança para proteger o material chave, o dispositivo não pode ser eliminado como recurso Azure até estar no estado zeroizado.

> [!NOTE]
> se tiver problemas com qualquer configuração do dispositivo Gemalto, deve contactar [o suporte ao cliente da Gemalto](https://safenet.gemalto.com/technical-support/).


Se terminou com todos os recursos deste grupo de recursos, então pode removê-los todos com o seguinte comando:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Passos seguintes

Após completar os passos no tutorial, são fornecidos recursos dedicados ao HSM e tem uma rede virtual com HSMs necessários e componentes de rede adicionais para permitir a comunicação com o HSM.  Está agora em posição de felicitar esta implantação com mais recursos, conforme exigido pela sua arquitetura de implantação preferida. Para obter mais informações sobre como ajudar a planear a sua implantação, consulte os documentos do Concepts.
Recomenda-se um desenho com dois HSMs numa região primária que aborde a disponibilidade ao nível da cremalheira, e dois HSMs numa região secundária que aborde a disponibilidade regional. O ficheiro de modelo utilizado neste tutorial pode ser facilmente usado como base para uma implantação de dois HSM, mas precisa de ter os seus parâmetros modificados para satisfazer os seus requisitos.

* [Alta Disponibilidade](high-availability.md)
* [Segurança Física](physical-security.md)
* [Redes](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)
