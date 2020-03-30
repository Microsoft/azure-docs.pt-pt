---
title: Azure HANA Grandes Instâncias controlam através do portal Azure [ Microsoft Docs
description: Descreve como pode identificar e interagir com as grandes instâncias do Azure HANA através do portal
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c14ff9c4f6d2bc2b1a62d1874d01950d09491c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70099822"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Controlo de Grandes Instâncias do Azure HANA através do portal do Azure
Este documento cobre a forma como as [grandes instâncias hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) são apresentadas no [portal Azure](https://portal.azure.com) e que atividades podem ser realizadas através do portal Azure com unidades HANA Large Instance que são implantadas para si. A visibilidade das grandes instâncias HANA no portal Azure é fornecida através de um fornecedor de recursos Azure para as grandes instâncias HANA, que atualmente está em pré-visualização pública

## <a name="register-hana-large-instance-resource-provider"></a>Registe o fornecedor de recursos de grande instância HANA
Normalmente, a sua subscrição Azure que estava a usar para implementações de grandes instâncias HANA está registada para o Fornecedor de Recursos de Grande Instância HANA. No entanto, se não conseguir ver que implantou unidades hana large instance, deve registar o Fornecedor de Recursos na sua subscrição Azure. Há duas maneiras de registar o fornecedor de recursos de grande instância HANA

### <a name="register-through-cli-interface"></a>Registe-se através da interface CLI
Precisa de ser registado na sua subscrição Azure, utilizada para a implementação de Big Instance HANA através da interface Azure CLI. Pode (re-)registar o Fornecedor de Grandes Instâncias HANA com este comando:
    
    az provider register --namespace Microsoft.HanaOnAzure

Para mais informações, consulte o artigo [Fornecedores e tipos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli) de recursos azure


### <a name="register-through-azure-portal"></a>Registe-se através do portal Azure
Pode (re-)registar o Fornecedor de Recursos de Grande Instância HANA através do portal Azure. Você precisa listar a sua subscrição no portal Azure e clicar duas vezes na subscrição, que foi usada para implementar a sua unidade de Instância Grande HANA. Um que você está na página geral da sua subscrição, selecione "Fornecedores de recursos" como mostrado abaixo e escreva "HANA" na janela de pesquisa. 

![Registe o HLI RP através do portal Azure](./media/hana-li-portal/portal-register-hli-rp.png)

Na imagem mostrada, o fornecedor de recursos já estava registado. Caso o prestador de recursos ainda não esteja registado, prima "re-registrar" ou "registar-se".

Para mais informações, consulte o artigo [Fornecedores e tipos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell) de recursos azure


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Exposição de unidades hana large instância no portal Azure
Ao submeter um pedido de implantação de grandes instâncias HANA, é-lhe pedido que especifique a subscrição Azure que também está a ligar às Grandes Instâncias HANA. Recomenda-se utilizar a mesma subscrição que está a utilizar para implantar a camada de aplicação SAP que funciona contra as unidades HANA Large Instance.
À medida que os seus primeiros Casos GRANDES HANA estão a ser implantados, um novo grupo de [recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) é criado na subscrição Azure que submeteu no pedido de implantação para a sua HANA Large Instance(s).  O novo grupo de recursos listará todas as suas unidades HANA Large Instance que implementou na subscrição específica.

Para encontrar o novo grupo de recursos Azure, você lista o grupo de recursos na sua subscrição navegando através do painel de navegação esquerdo do portal Azure

![Painel de navegação no portal Azure](./media/hana-li-portal/portal-resource-group.png)

Na lista de grupos de recursos, está a ser listado, pode ser necessário filtrar a subscrição que usou para ter as Grandes Instâncias HANA implantadas

![Filtrar grupos de recursos no portal Azure](./media/hana-li-portal/portal-filtering-subscription.png)

Depois de filtrar a subscrição correta, ainda pode ter uma longa lista de grupos de recursos. Procure um com uma pós-fixação de **-Txxx** onde "xxx" são três dígitos, como **-T050**. 

Como encontrou o grupo de recursos, enumere os detalhes do mesmo. A lista que recebeu pode parecer:

![Lista de HLI no portal Azure](./media/hana-li-portal/portal-hli-units-list.png)

Todas as unidades listadas representam uma única unidade HANA Large Instance que foi implantada na sua subscrição. Neste caso, você olha para oito diferentes unidades HANA Large Instance, que foram implantadas na sua subscrição.

Se você destacou vários inquilinos hana de grande instância sob a mesma subscrição Azure, você encontrará vários grupos de recursos Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Veja os atributos de uma única Unidade HLI
Na lista das unidades HANA Large Instance, pode clicar numa única unidade e obter os detalhes da única unidade HANA Large Instance. 

No ecrã geral, depois de clicar em 'Mostrar mais', está a receber uma apresentação da unidade, que parece:

![Mostrar visão geral de uma unidade HLI](./media/hana-li-portal/portal-show-overview.png)

Olhando para os diferentes atributos mostrados, esses atributos parecem pouco diferentes dos atributos do Azure VM. No cabeçalho do lado esquerdo, mostra o grupo Resource, a região azure, o nome da subscrição e o ID, bem como algumas tags que acrescentou. Por predefinição, as unidades HANA Large Instance não têm etiqueta atribuída. No lado direito do cabeçalho, o nome da unidade está listado como designado quando a colocação foi feita. O sistema operativo é mostrado, bem como o endereço IP. Tal como acontece com os VMs, o tipo de unidade de instância de grande instância HANA com o número de fios de CPU e memória também é mostrado. Mais detalhes sobre as diferentes unidades hana grandes instâncias, são mostrados aqui:

- [SKUs Disponíveis para HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Arquitetura de armazenamento SAP HANA (Grandes Instâncias)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Dados adicionais no lado inferior direito é a revisão do carimbo hana large instância. Os valores possíveis são:

- Revisão 3
- Revisão 4

A Revisão 4 é a mais recente arquitetura lançada de HANA Grandes Instâncias com grandes melhorias na latência da rede entre Os VMs Azure e HANA Grandes unidades de instância implantadas em selos ou linhas de revisão 4.
Outra informação muito importante encontra-se no canto inferior direito da visão geral com o nome do Grupo de Colocação de Proximidade azure que é automaticamente criado para cada unidade DE Instância Grande HANA implantada. Este Grupo de Colocação de Proximidade precisa de ser referenciado na implementação dos VMs Azure que acolhem a camada de aplicação SAP. Utilizando o grupo de colocação de [proximidade Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) associado à unidade HANA Large Instance, certifique-se de que os VMs Azure são implantados nas proximidades da unidade HANA Large Instance. A forma como os grupos de colocação de proximidade podem ser usados para localizar a camada de aplicação SAP no mesmo centro de dados Azure como a Revisão 4 unidades de grande instância hana hospedadas é descrita em Grupos de Colocação de [Proximidade Azure para uma latência de rede ótima com aplicações SAP](sap-proximity-placement-scenarios.md).

Um campo adicional na coluna direita do cabeçalho informa sobre o estado de potência da unidade de instância grande HANA.

> [!NOTE]
> O estado de energia descreve se a unidade de hardware está desligada ou desligada. Não dá informações sobre o sistema operativo estar a funcionar. Ao reiniciar uma unidade HANA Large Instance, sentirá um pequeno período em que o estado da unidade muda para **começar a** mudar-se para o estado de **Iniciado**. Estar no estado de **Iniciado** significa que o Sistema operativo está a começar ou que o Sistema operativo foi completamente iniciado. Como resultado, após um reinício da unidade, não pode esperar entrar imediatamente na unidade assim que o estado mudar para **Iniciado**.
> 

Se premir "Ver mais", são mostradas informações adicionais. Uma informação adicional é a revisão do carimbo hana large instance, a unidade foi implantada. Veja o artigo [O que é SAP HANA sobre Azure (Grandes Instâncias)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) para as diferentes revisões dos selos hana large instância

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Verifique as atividades de uma única unidade HANA Large Instance 
Além de dar uma visão geral das unidades HANA Large Instance, pode verificar as atividades da unidade em particular. Um registo de atividade pode parecer:

![Painel de navegação no portal Azure](./media/hana-li-portal/portal-activity-list.png)

Uma das principais atividades registadas são o reinício de uma unidade. Os dados listados incluem o estado da atividade, o carimbo de tempo da atividade foi desencadeado, o ID de subscrição do qual a atividade foi desencadeada e o utilizador azure que desencadeou a atividade. 

Outra atividade que está a ser registada são as alterações na unidade nos meta dados do Azure. Além do reinício iniciado, pode ver a atividade da **Write HANAInstances**. Este tipo de atividade não realiza alterações na própria unidade HANA Large Instance, mas está a documentar alterações nos meta dados da unidade em Azure. No caso listado, adicionámos e apagamos uma etiqueta (ver secção seguinte).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Adicione e elimine uma etiqueta Azure a uma unidade HANA Large Instance
Outra possibilidade que você tem é adicionar uma [etiqueta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) a uma unidade HANA Large Instance. A forma como as etiquetas estão a ser atribuídas não difere da atribuição de etiquetas a VMs. Tal como acontece com os VMs, as etiquetas existem em meta dados Azure e, para as grandes instâncias HANA, têm as mesmas restrições que as etiquetas para VMs.

As etiquetas de aparação funcionam da mesma forma que com os VMs. Ambas as atividades, aplicação e abater uma etiqueta serão listadas no registo de atividade da unidade de grande instância HANA.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Verifique as propriedades de uma unidade HANA Large Instance
A secção **Propriedades** inclui informações importantes que obtém quando as instâncias são entregues a si. É uma secção onde obtém toda a informação que pode necessitar em casos de suporte ou que precisa ao configurar a configuração do instantâneo de armazenamento. Como tal, esta secção é uma recolha de dados em torno da sua instância, a conectividade da instância com o Azure e o backend de armazenamento. O topo da secção parece:


![parte superior das propriedades hli no portal Azure](./media/hana-li-portal/portal-properties-top.png)

Os primeiros itens de dados já viram no ecrã geral. Mas uma parte importante dos dados é o ID do Circuito ExpressRoute, que obteve quando as primeiras unidades implantadas foram entregues. Em alguns casos de apoio, pode ser-lhe pedido esses dados. Uma entrada de dados importante é mostrada na parte inferior da imagem. Os dados apresentados são o endereço IP da cabeça de armazenamento NFS que isola o seu armazenamento ao seu **inquilino** na pilha de grandes instâncias HANA. Este endereço IP também é necessário quando edita o ficheiro de [configuração para cópias de segurança instantâneas](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)de armazenamento . 

À medida que percorre o painel de propriedades, obtém dados adicionais como um ID de recurso único para a sua unidade HANA Large Instance, ou o ID de subscrição que foi atribuído à implementação.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Reiniciar uma unidade HANA Large Instance através do portal Azure
Iniciando um reinício do sistema operativo Linux, houve várias situações em que o Sistema operativo não conseguiu terminar um reinício com sucesso. Para forçar um reinício, foi necessário abrir um pedido de serviço para que as operações da Microsoft realizassem um reinício de energia da unidade HANA Large Instance. A funcionalidade de um reinício de potência de uma unidade HANA Large Instance está agora integrada no portal Azure. Como está na parte geral da unidade HANA Large Instance, vê o botão para reiniciar em cima da secção de dados

![Reiniciar passo #1 no portal Azure](./media/hana-li-portal/portal-restart-first-step.png)

Ao premir o botão de reinício, é-lhe perguntado se realmente quer reiniciar a unidade. Como confirmar premindo o botão "Sim", a unidade reiniciará.

> [!NOTE]
> No processo de reinício, você vai experimentar um pequeno período em que o estado da unidade muda para **começar a** mover-se para o estado de **Iniciado**. Estar no estado de **Iniciado** significa que o Sistema operativo está a começar ou que o Sistema operativo foi completamente iniciado. Como resultado, após um reinício da unidade, não pode esperar entrar imediatamente na unidade assim que o estado mudar para **Iniciado**.

> [!IMPORTANT]
> Dependendo da quantidade de memória na sua unidade HANA Large Instance, um reinício e reinício do hardware e do sistema operativo pode demorar até uma hora


## <a name="open-a-support-request-for-hana-large-instances"></a>Abra um pedido de apoio para grandes instâncias HANA
Fora da exposição do portal Azure de unidades HANA Large Instance, você pode criar pedidos de suporte especificamente para uma unidade de grande instância HANA também. À medida que segue o link **Novo pedido** de suporte 

![iniciar passo de pedido de serviço #1 no portal Azure](./media/hana-li-portal/portal-initiate-support-request.png)

Para obter o serviço de SAP HANA Grandes Instâncias listados no próximo ecrã, você pode precisar selecionar 'All Services' como mostrado abaixo

![Selecione todos os serviços no portal Azure](./media/hana-li-portal/portal-create-service-request.png)

Na lista de serviços, pode encontrar o serviço **SAP HANA Large Instance**. Ao escolher esse serviço, pode selecionar tipos de problemas específicos como mostrado:


![Selecione classe de problemas no portal Azure](./media/hana-li-portal/portal-select-problem-class.png)

Em cada um dos diferentes tipos de problemas, é-lhe oferecida uma seleção de subtipos de problemas que precisa de selecionar para caracterizar ainda mais o seu problema. Depois de selecionar o subtipo, pode agora nomear o sujeito. Uma vez terminado o processo de seleção, pode passar para o próximo passo da criação. Na secção **Soluções,** é apontado para documentação em torno de HANA Grandes Instâncias, o que pode dar um ponteiro para uma solução do seu problema. Se não encontrar uma solução para o seu problema na documentação sugerida, vá para o próximo passo. No próximo passo, ser-lhe-ão questionados se o problema é com VMs ou com unidades HANA Large Instance. Esta informação ajuda a direcionar o pedido de apoio aos especialistas corretos. 

![Detalhes do caso de suporte no portal Azure](./media/hana-li-portal/portal-support-request-details.png)

À medida que respondia às perguntas e forneceu detalhes adicionais, pode dar o próximo passo para rever o pedido de apoio e submetê-lo.

## <a name="next-steps"></a>Passos seguintes

- [Como monitorizar o SAP HANA (grandes instâncias) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitorizar e resolver problemas do lado do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

