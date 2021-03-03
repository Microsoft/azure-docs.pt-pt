---
title: Azure HANA Grandes Instâncias controlam através do portal Azure | Microsoft Docs
description: Descreve como pode identificar e interagir com Azure HANA Grandes Instâncias através do portal
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/31/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d32adab35852cd19933fe3877c2742a21ddc5f1e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676981"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Controlo de Grandes Instâncias do Azure HANA através do portal do Azure

>[!NOTE]
>Para o Rev 4.2, siga as instruções nas [Instâncias Gestão BareMetal através do tópico do portal Azure.](../../../baremetal-infrastructure/workloads/sap/baremetal-infrastructure-portal.md)

Este documento cobre a forma como as [Grandes Instâncias da HANA](./hana-overview-architecture.md) são apresentadas no [portal Azure](https://portal.azure.com) e que atividades podem ser realizadas através do portal Azure com unidades de Grande Instância HANA que são implantadas para si. A visibilidade de HANA Large Instances in Azure portal é fornecida através de um fornecedor de recursos Azure para HANA Large Instances, que atualmente está em pré-visualização pública

## <a name="register-hana-large-instance-resource-provider"></a>Registar fornecedor de recursos de grande instância HANA
Normalmente, a sua subscrição Azure que estava a usar para implementações HANA Large Instance está registada para o Fornecedor de Recursos de Grande Instância HANA. No entanto, se não conseguir ver que implementou unidades HANA Large Instance, deverá registar o Fornecedor de Recursos na sua subscrição Azure. Existem duas formas de registar o fornecedor de recursos de grande instância HANA

### <a name="register-through-cli-interface"></a>Registe-se através da interface CLI
Tem de iniciar sessão na sua subscrição Azure, utilizada para a implementação da HANA Large Instance através da interface Azure CLI. Pode (re-)registar o Fornecedor de Grandes Instâncias HANA com este comando:
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

Para mais informações, consulte o artigo [Fornecedores e tipos de recursos Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)


### <a name="register-through-azure-portal"></a>Registe-se através do portal Azure
Pode (re-)registar o Fornecedor de Recursos de Grande Instância HANA através do portal Azure. Tem de listar a sua subscrição no portal Azure e clicar duas vezes na subscrição, que foi usada para implantar a sua unidade(s) HANA Large Instance. Um dos que está na página geral da sua subscrição, selecione "Fornecedores de recursos" como mostrado abaixo e digite "HANA" na janela de pesquisa. 

![Registar HLI RP através do portal Azure](./media/hana-li-portal/portal-register-hli-rp.png)

Na imagem mostrada, o fornecedor de recursos já estava registado. Caso o fornecedor de recursos ainda não esteja registado, prima "re-registrar" ou "registar-se".

Para mais informações, consulte o artigo [Fornecedores e tipos de recursos Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Exibição de unidades de grande instância HANA no portal Azure
Ao submeter um pedido de implantação HANA Large Instance, é-lhe pedido que especifique a subscrição Azure que também está a ligar às Grandes Instâncias HANA. Recomenda-se que utilize a mesma subscrição que está a utilizar para implantar a camada de aplicação SAP que funciona contra as unidades HANA Large Instance.
À medida que as suas primeiras HANA Large Instances estão a ser implementadas, um novo [grupo de recursos Azure](../../../azure-resource-manager/management/manage-resources-portal.md) é criado na subscrição Azure que submeteu no pedido de implantação do seu(s) HANA Large Instance(s).  O novo grupo de recursos irá listar todas as suas unidades HANA Large Instance que implementou na subscrição específica.

Para encontrar o novo grupo de recursos Azure, lista o grupo de recursos na sua subscrição navegando através do painel de navegação esquerdo do portal Azure

![Screenshot que realça a opção Grupos de Recursos.](./media/hana-li-portal/portal-resource-group.png)

Na lista de grupos de recursos, está a ser listado, pode ter de filtrar a subscrição que usou para ter HANA Large Instances implantada

![Grupos de recursos de filtro no portal Azure](./media/hana-li-portal/portal-filtering-subscription.png)

Depois de filtrar para a subscrição correta, ainda pode ter uma longa lista de grupos de recursos. Procure um com uma pós-correcção de **-Txxx** onde "xxx" são três dígitos, como **-T050**. 

Ao encontrar o grupo de recursos, enuprosse os detalhes. A lista que recebeu pode parecer:

![Lista HLI no portal Azure](./media/hana-li-portal/portal-hli-units-list.png)

Todas as unidades listadas representam uma única unidade HANA Large Instance que foi implantada na sua subscrição. Neste caso, você olha para oito unidades diferentes HANA Large Instance, que foram implantadas na sua subscrição.

Se você implantou vários inquilinos HANA Large Instance sob a mesma assinatura Azure, você encontrará vários grupos de recursos Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Veja os atributos de unidade HLI única
Na lista das unidades HANA Large Instance, pode clicar numa única unidade e chegar aos detalhes da unidade única HANA Large Instance. 

No ecrã geral, depois de clicar em 'Mostrar mais', está a receber uma apresentação da unidade, que parece:

![Mostrar visão geral de uma unidade HLI](./media/hana-li-portal/portal-show-overview.png)

Olhando para os diferentes atributos mostrados, esses atributos dificilmente são diferentes dos atributos Azure VM. No cabeçalho do lado esquerdo, mostra o grupo de Recursos, região Azure, nome de subscrição e ID, bem como algumas etiquetas que adicionou. Por predefinição, as unidades HANA Large Instance não têm identificação. No lado direito do cabeçalho, o nome da unidade é listado como atribuído quando a colocação foi feita. O sistema operativo é mostrado, bem como o endereço IP. Tal como acontece com os VMs, o tipo de unidade HANA Large instance com o número de fios de CPU e memória também é mostrado. Mais detalhes sobre as diferentes unidades de Grande Instância HANA, são mostrados aqui:

- [SKUs Disponíveis para HLI](./hana-available-skus.md)
- [Sap HANA (Grandes Instâncias) arquitetura de armazenamento](./hana-storage-architecture.md) 

Dados adicionais no lado direito inferior é a revisão do carimbo HANA Large Instance. Os valores possíveis são:

- Revisão 3
- Revisão 4

A Revisão 4 é a mais recente arquitetura lançada de HANA Large Instances com grandes melhorias na latência da rede entre Azure VMs e HANA Unidades de grande instância implantadas na Revisão 4 selos ou linhas.
Outra informação muito importante encontra-se no canto inferior direito da visão geral com o nome do Grupo de Colocação de Proximidade Azure que é automaticamente criado para cada unidade de Grande Instância HANA implantada. Este Grupo de Colocação de Proximidade tem de ser referenciado ao implementar os VMs Azure que acolhem a camada de aplicação SAP. Ao utilizar o [grupo de colocação de proximidade Azure](../../co-location.md) associado à unidade HANA Large Instance, certifique-se de que os VMs Azure são implantados nas proximidades da unidade HANA Large Instance. A forma como os grupos de colocação de proximidade podem ser usados para localizar a camada de aplicação SAP no mesmo centro de dados Azure como revisão 4 unidades HANA Large Instance é descrita em [Grupos de Colocação de Proximidade Azure para uma latência de rede ideal com aplicações SAP](sap-proximity-placement-scenarios.md).

Um campo adicional na coluna direita do cabeçalho informa sobre o estado de potência da unidade HANA Large.

> [!NOTE]
> O estado de alimentação descreve se a unidade de hardware está ligado ou desligado. Não dá informações sobre o funcionamento do sistema operativo. Ao reiniciar uma unidade HANA Large Instance, irá experimentar um pequeno período em que o estado da unidade muda para **começar a** mover-se para o estado de **Iniciado**. Estar no estado de **Início** significa que o SO está a começar ou que o SO foi completamente iniciado. Como resultado, após um reinício da unidade, não pode esperar iniciar imediatamente o login na unidade assim que o estado mudar para **Iniciar**.
> 

Se premir 'Ver mais', são apresentadas informações adicionais. Uma informação adicional é exibir a revisão do carimbo HANA Large Instance, a unidade foi implantada. Veja o artigo [O que é SAP HANA em Azure (Grandes Instâncias)](./hana-overview-architecture.md) para as diferentes revisões dos selos HANA Large Instance

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Verifique as atividades de uma única unidade HANA Large Instance 
Além de dar uma visão geral das unidades HANA Large Instance, você pode verificar as atividades da unidade em particular. Um registo de atividade pode parecer:

![Painel de navegação no portal Azure](./media/hana-li-portal/portal-activity-list.png)

Uma das principais atividades registadas são o recomeço de uma unidade. Os dados listados incluem o estado da atividade, o carimbo de tempo que a atividade foi desencadeada, o ID de subscrição do qual a atividade foi desencadeada e o utilizador Azure que desencadeou a atividade. 

Outra atividade que está a ser registada são as alterações à unidade nos dados de meta do Azure. Além do recomeço iniciado, pode ver a atividade de **Write HANAInstances**. Este tipo de atividade não realiza alterações na própria unidade HANA Large Instance, mas está a documentar alterações nos dados de meta da unidade em Azure. No caso listado, adicionámos e apagamos uma etiqueta (ver secção seguinte).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Adicione e elimine uma etiqueta Azure a uma unidade HANA Large Instance
Outra possibilidade que você tem é adicionar uma [etiqueta](../../../azure-resource-manager/management/tag-resources.md) a uma unidade HANA Large Instance. A forma como as etiquetas estão a ser atribuídas não difere da atribuição de etiquetas a VMs. Tal como acontece com os VMs, as etiquetas existem nos dados metaure do Azure e, para as Grandes Instâncias HANA, têm as mesmas restrições que as etiquetas para os VMs.

Eliminar etiquetas funciona da mesma forma que com os VMs. Ambas as atividades, aplicando e eliminando uma etiqueta serão listadas no registo de atividades da unidade particular HANA Large Instance.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Verifique as propriedades de uma unidade HANA Large Instance
A secção **Propriedades** inclui informações importantes que obtém quando as ocorrências são entregues a si. É uma secção onde obtém todas as informações que pode necessitar em casos de suporte ou que necessita ao configurar a configuração do instantâneo de armazenamento. Como tal, esta secção é uma recolha de dados em torno do seu caso, a conectividade da instância para Azure e o backend de armazenamento. O topo da secção parece:


![parte superior das propriedades do HLI no portal Azure](./media/hana-li-portal/portal-properties-top.png)

Os primeiros itens de dados já viram no ecrã geral. Mas uma parte importante dos dados é o ID do Circuito ExpressRoute, que obteve quando as primeiras unidades implantadas foram entregues. Em alguns casos de apoio, pode ser-lhe pedido esses dados. Uma entrada importante de dados é mostrada na parte inferior da imagem. Os dados apresentados são o endereço IP da cabeça de armazenamento NFS que isola o seu armazenamento ao seu **inquilino** na pilha HANA Large Instance. Este endereço IP também é necessário quando edita o [ficheiro de configuração para cópias de segurança instantâneas de armazenamento](./hana-backup-restore.md#set-up-storage-snapshots). 

Ao deslocar-se para baixo no painel de propriedades, obtém dados adicionais como um ID de recurso único para a sua unidade HANA Large Instance, ou o ID de subscrição que foi atribuído à implementação.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Reinicie uma unidade de grande instância HANA através do portal Azure
Iniciando um reinício do sistema operativo Linux, houve várias situações em que o SISTEMA não conseguiu terminar um recomeço com sucesso. Para forçar o reinício, foi necessário abrir um pedido de serviço para que as operações da Microsoft realizassem um reinício de energia da unidade HANA Large Instance. A funcionalidade de um reinício de energia de uma unidade HANA Large Instance está agora integrada no portal Azure. Como está na parte geral da unidade HANA Large Instance, vê o botão para reiniciar em cima da secção de dados

![Reiniciar passo #1 no portal Azure](./media/hana-li-portal/portal-restart-first-step.png)

Ao premir o botão de reinício, perguntam-lhe se pretende mesmo reiniciar a unidade. Como confirma premindo o botão "Sim", a unidade reiniciará.

> [!NOTE]
> No processo de reinício, irá experimentar um pequeno período em que o estado da unidade muda para **Começar a** mover-se para o estado de **Início**. Estar no estado de **Início** significa que o SO está a começar ou que o SO foi completamente iniciado. Como resultado, após um reinício da unidade, não pode esperar iniciar imediatamente o login na unidade assim que o estado mudar para **Iniciar**.

> [!IMPORTANT]
> Dependente da quantidade de memória na sua unidade HANA Large Instance, um reinício e reinicialização do hardware e do sistema operativo pode demorar até uma hora


## <a name="open-a-support-request-for-hana-large-instances"></a>Abrir um pedido de apoio para HANA grandes instâncias
Fora da exibição do portal Azure de unidades HANA Large Instance, você pode criar pedidos de suporte especificamente para uma unidade HANA grande Instância também. À medida que segue o link **Novo pedido de apoio** 

![iniciar pedido de serviço #1 no portal Azure](./media/hana-li-portal/portal-initiate-support-request.png)

Para obter o serviço de SAP HANA Large Instances listado no ecrã seguinte, poderá ter de selecionar 'Todos os Serviços' como mostrado abaixo

![Selecione todos os serviços no portal Azure](./media/hana-li-portal/portal-create-service-request.png)

Na lista de serviços, pode encontrar o serviço **SAP HANA Large Instance**. Ao escolher este serviço, pode selecionar tipos específicos de problemas como mostrado:


![Selecione classe de problemas no portal Azure](./media/hana-li-portal/portal-select-problem-class.png)

Em cada um dos diferentes tipos de problemas, é-lhe oferecida uma seleção de subtipos problemáticos que precisa de selecionar para caracterizar ainda mais o seu problema. Depois de selecionar o subtipo, pode agora nomear o sujeito. Uma vez terminada a fase de seleção, pode passar ao próximo passo da criação. Na secção **Soluções,** é apontado para documentação em torno de HANA Large Instances, o que pode dar um ponteiro para uma solução do seu problema. Se não encontrar uma solução para o seu problema na documentação sugerida, vá para o próximo passo. No próximo passo, perguntar-lhe-ão se a questão é com VMs ou com unidades de Grande Instância HANA. Estas informações ajudam a direcionar o pedido de apoio aos especialistas corretos. 

![Detalhes do caso de apoio no portal Azure](./media/hana-li-portal/portal-support-request-details.png)

À medida que respondeu às perguntas e forneceu detalhes adicionais, pode dar o passo seguinte para rever o pedido de apoio e submetê-lo.

## <a name="next-steps"></a>Passos seguintes

- [Como monitorizar o SAP HANA (grandes instâncias) em Azure](./troubleshooting-monitoring.md)
- [Monitorizar e resolver problemas do lado do HANA](./hana-monitor-troubleshoot.md)