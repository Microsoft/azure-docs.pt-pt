---
title: Tutorial implanta-se numa rede virtual existente utilizando o Azure CLI - Azure Dedicado HSM [ Microsoft Docs
description: Tutorial mostrando como implementar um HSM dedicado usando o CLI em uma rede virtual existente
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
ms.openlocfilehash: 76b7a97a5be5e7952b0ac11d93bd68656ff8f1ec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79454317"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-cli"></a>Tutorial: Implantação de HSMs numa rede virtual existente utilizando cli

A Azure Dedicated HSM fornece um dispositivo físico para uso exclusivo do cliente, com total controlo administrativo e plena responsabilidade de gestão. A utilização de dispositivos físicos cria a necessidade de a Microsoft controlar a alocação de dispositivos para garantir que a capacidade é gerida de forma eficaz. Como resultado, no âmbito de uma subscrição do Azure, o serviço HSM dedicado não será normalmente visível para o fornecimento de recursos. Qualquer cliente Azure que necessite de acesso ao serviço HSM dedicado, deve primeiro contactar o seu executivo de conta Microsoft para solicitar o registo do serviço HSM dedicado. Só quando este processo estiver concluído com êxito será possível o fornecimento. 

Este tutorial mostra um processo típico de provisionamento onde:

- Um cliente já tem uma rede virtual
- Têm uma máquina virtual.
- Precisam de adicionar recursos HSM a esse ambiente existente.

Uma arquitetura típica, de alta disponibilidade, de implantação multi-região pode parecer a seguinte:

![implantação de várias regiões](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

Este tutorial centra-se num par de HSMs e exigiu que o ExpressRoute Gateway (ver Subnet 1 acima) fosse integrado numa rede virtual existente (ver VNET 1 acima).  Todos os outros recursos são recursos standard Azure. O mesmo processo de integração pode ser utilizado para HSMs na sub-rede 4 no VNET 3 acima.

## <a name="prerequisites"></a>Pré-requisitos

O Azure Dedicado HSM não está disponível no portal Azure. Toda a interação com o serviço será via linha de comando ou utilizando o PowerShell. Este tutorial utilizará a interface de linha de comando (CLI) na Casca de Nuvem Azure. Se é novo no Azure CLI, siga as instruções de início aqui: [Azure CLI 2.0 Get Started](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

Pressupostos:

- Completou o processo de registo DeHSM dedicado ao Azure
- Foi aprovado para uso do serviço. Caso contrário, contacte o representante da sua conta microsoft para obter mais informações.
- Criou um Grupo de Recursos para estes recursos e os novos implantados neste tutorial juntar-se-ão a esse grupo.
- Já criou a rede virtual necessária, subnet e máquinas virtuais de acordo com o diagrama acima e agora quer integrar 2 HSMs nessa implementação.

Todas as instruções abaixo assumem que já navegou para o portal Azure e abriu a Cloud Shell (selecione "\>\_em direção à direita superior do portal).

## <a name="provisioning-a-dedicated-hsm"></a>Provisionamento de um HSM dedicado

O fornecimento de HSMs e a sua integração numa rede virtual existente através do ExpressRoute Gateway serão validados através do SSH. Esta validação ajuda a garantir a capacidade de acesso e disponibilidade básica do dispositivo HSM para quaisquer outras atividades de configuração. Os seguintes comandos usarão um modelo de Gestor de Recursos Azure para criar os recursos HSM e recursos de rede associados.

### <a name="validating-feature-registration"></a>Validação do Registo de Recursos

Como mencionado acima, qualquer atividade de provisionamento requer que o serviço HSM dedicado esteja registado para a sua subscrição. Para validar isso, execute os seguintes comandos na concha de nuvem do portal Azure.

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

O comando seguinte verifica as funcionalidades de rede necessárias para o serviço HSM dedicado.

```azurecli
az feature show \
   --namespace Microsoft.Network \
   --name AllowBaremetalServers
```

Ambos os comandos devem devolver um estatuto de "Registado" (como mostrado abaixo). Se os comandos não devolverem "Registado" tem de se registar para este serviço, contacte o seu representante da conta microsoft.

![estatuto de subscrição](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>Criação de recursos HSM

Um HSM é provisionado na rede virtual de um cliente para que uma rede virtual e subnet sejam necessárias. Uma dependência para o HSM permitir a comunicação entre a rede virtual e o dispositivo físico é um Gateway ExpressRoute, e finalmente uma máquina virtual é necessária para aceder ao dispositivo HSM usando o software cliente Gemalto. Estes recursos foram recolhidos num ficheiro de modelo, com ficheiro de parâmetro correspondente, para facilitar a utilização. Os ficheiros estão disponíveis contactando diretamente a Microsoft como HSMrequest@Microsoft.com.

Uma vez que tenha os ficheiros, tem de editar o ficheiro parâmetro para inserir os seus nomes preferidos para recursos. Editar linhas com "valor": "".

- `namingInfix`Prefixo para nomes de recursos HSM
- `ExistingVirtualNetworkName`Nome da rede virtual utilizada para os HSMs
- `DedicatedHsmResourceName1`Nome do recurso HSM no carimbo do datacenter 1
- `DedicatedHsmResourceName2`Nome do recurso HSM no carimbo do datacenter 2
- `hsmSubnetRange`Gama de endereços IP subnet para HSMs
- `ERSubnetRange`Gama de endereços IP subnet para gateway VNET

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

O ficheiro de modelo do Gestor de Recursos Azure associado criará 6 recursos com esta informação:

- Uma sub-rede para os HSMs no VNET especificado
- Uma subnet para o gateway da rede virtual
- Um portal de rede virtual que liga o VNET aos dispositivos HSM
- Um endereço IP público para o portal
- Um HSM no carimbo 1
- Um HSM no carimbo 2

Uma vez definidos os valores dos parâmetros, os ficheiros precisam de ser enviados para a partilha de ficheiros cloud do portal Azure para utilização. No portal Azure, clique\>\_no " símbolo da casca de nuvem em cima à direita e isso fará da parte inferior do ecrã um ambiente de comando. As opções para isso são BASH e PowerShell e você deve selecionar BASH se ainda não definido.

A concha de comando tem uma opção de upload/download na barra de ferramentas e deve selecionar isto para carregar o modelo e os ficheiros de parâmetros para a sua partilha de ficheiros:

![partilha de ficheiros](media/tutorial-deploy-hsm-cli/file-share.png)

Uma vez carregados os ficheiros, está pronto para criar recursos. Antes de criar novos recursos HSM, existem alguns recursos pré-necessários que deve garantir que estão em vigor. Deve ter uma rede virtual com gamas de sub-redes para computação, HSMs e gateway. Os seguintes comandos servem como um exemplo do que criaria uma rede virtual.

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16
  --subnet-name compute
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet create \
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
>A configuração mais importante a notar para a rede virtual é que a sub-rede para o dispositivo HSM deve ter delegações definidas para "Microsoft.HardwareSecurityModules/dedicadosHSMs".  O fornecimento de HSM não funcionará sem que esta opção seja definida.

Uma vez que todos os pré-requisitos estejam no lugar, execute o seguinte comando para usar o modelo do Gestor de Recursos Azure garantindo que você tem valores atualizados com os seus nomes únicos (pelo menos o nome do grupo de recursos):

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

Quando a implementação completar com sucesso "provisioningState": "Succeeded" será exibida. Pode ligar-se à sua máquina virtual existente e utilizar o SSH para garantir a disponibilidade do dispositivo HSM.

## <a name="verifying-the-deployment"></a>Verificação da Implantação

Para verificar se os dispositivos foram aprovisionados e ver os atributos do dispositivo, execute o seguinte conjunto de comandos. Certifique-se de que o grupo de recursos está definido adequadamente e o nome do recurso é exatamente como você tem no ficheiro parâmetro.

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

![produção de provisionamento](media/tutorial-deploy-hsm-cli/progress-status2.png)

Você também poderá agora ver os recursos usando o explorador de [recursos Azure.](https://resources.azure.com/)   Uma vez no explorador, expanda "subscrições" à esquerda, expanda a sua subscrição específica para HSM dedicado, expanda "grupos de recursos", expanda o grupo de recursos que utilizou e finalmente selecione o item de "recursos".

## <a name="testing-the-deployment"></a>Testar a Implantação

Testar a implementação é um caso de ligação a uma máquina virtual que pode aceder ao HSM(s) e, em seguida, ligar-se diretamente ao dispositivo HSM. Estas ações confirmarão que o HSM é acessível.
A ferramenta ssh é usada para ligar à máquina virtual. O comando será semelhante ao seguinte, mas com o nome do administrador e o nome dns que especificano no parâmetro.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

O endereço IP do VM também poderia ser utilizado no lugar do nome DNS no comando acima. Se o comando for bem sucedido, irá pedir uma senha e deve introduzir-a. Uma vez iniciado sessão na máquina virtual, pode iniciar sessão no HSM utilizando o endereço IP privado encontrado no portal para o recurso de interface de rede associado ao HSM.

![lista de componentes](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>Note a caixa de verificação "Mostrar tipos ocultos", que quando selecionado apresentará recursos HSM.

Na imagem acima, clicar no "HSM1_HSMnic" ou "HSM2_HSMnic" mostraria o endereço IP privado apropriado. Caso contrário, `az resource show` o comando utilizado acima é uma forma de identificar o endereço IP certo. 

Quando tiver o endereço IP correto, execute o seguinte comando substituindo esse endereço:

`ssh tenantadmin@10.0.2.4`

Se tiver sucesso, será solicitado por uma senha. A palavra-passe padrão é password e o HSM pedir-lhe-á primeiro que altere a sua palavra-passe para definir uma palavra-passe forte e utilizar qualquer mecanismo que a sua organização prefira armazenar a palavra-passe e evitar perdas.

>[!IMPORTANT]
>se perder esta senha, o HSM terá de ser reiniciado e isso significa perder as chaves.

Quando estiver ligado ao HSM utilizando ssh, execute o seguinte comando para garantir que o HSM está operacional.

`hsm show`

A saída deve parecer como mostrado na imagem abaixo:

![lista de componentes](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

Neste momento, atribuiu todos os recursos para um estado altamente disponível, dois HSM e validado o acesso e o estado operacional. Qualquer configuração ou teste adicional envolve mais trabalho com o próprio dispositivo HSM. Para isso, deve seguir as instruções no Guia de Administração Da Rede Gemalto Luna HSM 7 para inicializar o HSM e criar divisórias. Toda a documentação e software estão disponíveis diretamente a partir de Gemalto para download uma vez que você está registrado no Portal de Apoio ao Cliente Gemalto e tem um ID do cliente. Baixe a versão 7.2 do Client Software para obter todos os componentes necessários.

## <a name="delete-or-clean-up-resources"></a>Eliminar ou limpar recursos

Se terminou apenas com o dispositivo HSM, então pode ser apagado como um recurso e devolvido à piscina gratuita. A preocupação óbvia ao fazê-lo são quaisquer dados sensíveis do cliente que estão no dispositivo. A melhor maneira de "zeroizar" um dispositivo é obter a senha de administração HSM errada 3 vezes (nota: isto não é administração de aparelhos, é a administração hSM real). Como medida de segurança para proteger o material-chave, o dispositivo não pode ser eliminado como um recurso Azure até que esteja em estado zeroizado.

> [!NOTE]
> se tiver algum problema com qualquer configuração do dispositivo Gemalto, deve contactar o apoio ao [cliente da Gemalto.](https://safenet.gemalto.com/technical-support/)


Se já terminou com todos os recursos deste grupo de recursos, então pode removê-los todos com o seguinte comando:

```azurecli
az group deployment delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose

```

## <a name="next-steps"></a>Passos seguintes

Após completar os passos no tutorial, os recursos HSM dedicados são aprovisionados e você tem uma rede virtual com HSMs necessários e outros componentes de rede para permitir a comunicação com o HSM.  Está agora em condições de elogiar esta implantação com mais recursos, conforme exigido pela sua arquitetura de implantação preferida. Para obter mais informações sobre como ajudar a planear a sua implementação, consulte os documentos concepts.
Recomenda-se um desenho com dois HSMs numa região primária que aborde a disponibilidade ao nível do rack, e dois HSMs numa região secundária que aborde a disponibilidade regional. O ficheiro de modelo utilizado neste tutorial pode ser facilmente usado como base para uma implementação de dois HSM, mas precisa de ter os seus parâmetros modificados para satisfazer os seus requisitos.

* [Alta Disponibilidade](high-availability.md)
* [Segurança Física](physical-security.md)
* [Redes](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)
