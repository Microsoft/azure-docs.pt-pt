---
title: Instâncias grandes do HANA do Azure controle por meio do portal do Azure | Documentos da Microsoft
description: Descreve como a forma, pode identificar e interagir com instâncias grandes do HANA do Azure através do portal
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b186aa2692033a774d1d8f294315fcc0f5e874d5
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58763254"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Controlo de HANA nas instâncias grandes do Azure através do portal do Azure
Este documento aborda o forma como [instâncias grandes do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) são apresentados na [portal do Azure](https://portal.azure.com) e as atividades podem ser realizadas através do portal do Azure com as unidades de instância grande do HANA que são implementados por si. Visibilidade das instâncias grandes do HANA no portal do Azure é fornecida através de um fornecedor de recursos do Azure para instâncias grandes do HANA, que está atualmente em pré-visualização pública

## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Exibição de unidades de instância grande do HANA no portal do Azure
Ao submeter um pedido de implementação de instância grande do HANA, é-lhe perguntado para especificar a subscrição do Azure que está a ligar a instâncias grandes do HANA também. Recomenda-se para utilizar a mesma subscrição que está a utilizar para implementar a camada de aplicativo SAP que funciona com as unidades de instância grande do HANA.
Como o primeiro são introdução implementadas instâncias grandes do HANA, uma nova [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal) é criado na subscrição do Azure submetidas o pedido de implementação para a sua instância ou instâncias grandes do HANA.  Novo grupo de recursos lista todas as suas unidades de instância grande do HANA que implementou na subscrição específica.

Para localizar o novo grupo de recursos do Azure, lista o grupo de recursos na sua subscrição ao navegar por meio do painel de navegação à esquerda do portal do Azure

![Painel de navegação no portal do Azure](./media/hana-li-portal/portal-resource-group.png)

Na lista de grupos de recursos, está a ser listado, poderá ter de filtrar pela subscrição que utilizou para ter instâncias grandes do HANA implementado

![Filtrar grupos de recursos no portal do Azure](./media/hana-li-portal/portal-filtering-subscription.png)

Depois de filtragem para a subscrição correta, talvez seja necessário uma longa lista de grupos de recursos. Procure um com uma pós-correção dos **- Txxx** em que "xxx" é três dígitos, como **-T050**. 

Como já foi o grupo de recursos, liste os detalhes do mesmo. A lista que recebeu poderia ter o seguinte aspeto:

![Lista HLI no portal do Azure](./media/hana-li-portal/portal-hli-units-list.png)

Todas as unidades listadas são que representa uma única unidade de instância grande do HANA que tenha sido implementada na sua subscrição. Neste caso, examinar oito instâncias grandes do HANA unidades diferentes, que foram implementadas na sua subscrição.


## <a name="look-at-attributes-of-single-hli-unit"></a>Examinar os atributos de uma única unidade de HLI
Na lista de unidades de instância grande do HANA, pode clicar numa única unidade e obter os detalhes da unidade única instância grande do HANA. 

No ecrã de descrição geral, está a obter uma apresentação da unidade, o que é semelhante a:

![Mostrar descrição geral de uma unidade HLI](./media/hana-li-portal/portal-show-overview.png)

Examinando os atributos diferentes mostrados, esses atributos parecem muito diferentes de atributos de VM do Azure. No cabeçalho do lado do lado esquerdo, mostra o grupo de recursos, região do Azure, o nome da subscrição e ID, bem como algumas marcas que adicionou. Por predefinição, as unidades de instância grande do HANA não têm nenhuma marca atribuída. No lado direito do cabeçalho, o nome da unidade está listado como atribuído quando a implementação foi concluída. O sistema operativo é mostrado, bem como o endereço IP. Como com as VMs escreva a unidade de instância grande do HANA com o número de CPU threads e memória é mostrado também. Obter mais detalhes sobre as diferentes unidades de instância grande do HANA, são mostrados aqui:

- [SKUs Disponíveis para HLI](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [Arquitetura de armazenamento do SAP HANA (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

Um campo adicional na coluna da direita do cabeçalho informa sobre o estado de energia da unidade de instância grande do HANA.

> [!NOTE]
> O estado de energia descreve se a unidade de hardware seja ativada ou desativado. Ele não dá informações sobre o sistema operacional a ser a cópia de segurança e em execução. Como reiniciar uma unidade de instância grande do HANA, terá um tempo de pequenas onde o estado da unidade é alterado para **inicial** para mover para o estado dos **iniciado**. Está no estado dos **iniciado** significa que o sistema operacional está a ser iniciado ou que o sistema operacional tem sido iniciado completamente. Assim, após um reinício da unidade, não pode esperar entrar imediatamente a unidade assim que o estado muda para **iniciado**.
> 


## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Atividades de verificação de uma única unidade de instância grande do HANA 
Para além de fornecer uma visão geral das unidades de instância grande do HANA, pode verificar as atividades da unidade específica. Um registo de atividade pode ter o seguinte aspeto:

![Painel de navegação no portal do Azure](./media/hana-li-portal/portal-activity-list.png)

Uma das principais atividades registradas são reinícios de uma unidade. Os dados apresentados incluem o estado da atividade, o carimbo de hora a atividade foi acionada, o ID de subscrição de que a atividade foi acionada e o utilizador do Azure que acionou a atividade. 

Outra atividade que está a obter gravada são alterações para a unidade de dados do Azure meta. Além do reinício iniciado, pode ver a atividade de **escrever HANAInstances**. Este tipo de atividade não executa nenhuma alteração na unidade de instância grande do HANA em si, mas é documentar as alterações dos dados de metadados da unidade no Azure. No caso listado, adicionamos e eliminar uma etiqueta (consulte a secção seguinte).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Adicionar e eliminar uma etiqueta do Azure para uma unidade de instância grande do HANA
Outra possibilidade tiver é adicionar uma [marca](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) para uma unidade de instância grande do HANA. A forma como as etiquetas são introdução atribuídas não diferem dos atribuir etiquetas a VMs. Como com as VMs que as etiquetas existem nos metadados do Azure e, para instâncias grandes do HANA, tem as mesmas restrições que as etiquetas para as VMs.

A eliminar etiquetas funciona da mesma forma como acontece com VMs. As duas atividades, aplicando e eliminar uma etiqueta serão listadas no registo de atividades da unidade de instância grande do HANA específico.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Verifique as propriedades de uma unidade de instância grande do HANA
A secção **propriedades** inclui informações importantes que obtém quando as instâncias são enviadas a. É uma secção onde pode obter todas as informações que pode exigir suporte casos ou que precisa ao configurar a configuração de armazenamento de instantâneos. Como tal, esta secção é uma coleção de dados em torno da sua instância, a conectividade da instância do Azure e o back-end de armazenamento. Parte superior da seção é semelhante a:


![parte superior das propriedades HLI no portal do Azure](./media/hana-li-portal/portal-properties-top.png)

Os primeiros itens de dados alguns, viu no ecrã de descrição geral já. Mas uma parte importante de dados é o ID de circuito do ExpressRoute, que obtém de obteve as unidades implementadas primeiro passadas. Em alguns casos de suporte, pode obter solicitado para esses dados. Uma entrada de dados importantes é mostrada na parte inferior da tela. Os dados apresentados são o endereço IP do cabeçalho de armazenamento NFS que isola seu armazenamento para sua **inquilino** na pilha de instância grande do HANA. Este endereço IP também é necessário ao editar a [instantâneo de ficheiro de configuração para o armazenamento de cópias de segurança](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots). 

Como se deslocar para baixo no painel de propriedade, obtém dados adicionais, como um ID de recurso exclusiva para a unidade de instância grande do HANA ou o ID de subscrição que foi atribuído para a implementação.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Reiniciar uma unidade de instância grande do HANA através do portal do Azure
Iniciar um reinício do sistema operativo Linux, havia várias situações em que o sistema operacional não foi possível concluir um reinício com êxito. Para forçar um reinício, é necessária para abrir um pedido de serviço para que as operações de Microsoft efetuar um reinício de energia da unidade de instância grande do HANA. A funcionalidade de um reinício de energia de uma unidade de instância grande do HANA agora está integrada no portal do Azure. Como a parte de descrição geral da unidade de instância grande do HANA, verá o botão para reinicialização sobre a secção de dados

![Reinicie o passo #1 no portal do Azure](./media/hana-li-portal/portal-restart-first-step.png)

À medida que está nos pressionando o botão de reinício, é-lhe perguntado se realmente deseja reiniciar a unidade. Como confirmar pressionando o botão "Sim", a unidade será reiniciado.

> [!NOTE]
> O processo de reinício, terá um tempo de pequenas onde o estado da unidade é alterado para **inicial** para mover para o estado dos **iniciado**. Está no estado dos **iniciado** significa que o sistema operacional está a ser iniciado ou que o sistema operacional tem sido iniciado completamente. Assim, após um reinício da unidade, não pode esperar entrar imediatamente a unidade assim que o estado muda para **iniciado**.


## <a name="open-a-support-request-for-hana-large-instances"></a>Abra um pedido de suporte para as instâncias grandes do HANA
Fora do ecrã portal do Azure de unidades de instância grande do HANA, pode criar pedidos de suporte especificamente para uma HANA grandes instância unidade também. À medida que seguir a ligação **novo pedido de suporte** 

![iniciar o passo de pedido de serviço #1 no portal do Azure](./media/hana-li-portal/portal-initiate-support-request.png)

Para obter o serviço de SAP HANA nas instâncias grandes listados no ecrã seguinte, poderá ter de selecionar "todos os serviços" conforme mostrado abaixo

![Selecione todos os serviços no portal do Azure](./media/hana-li-portal/portal-create-service-request.png)

Na lista de serviços, pode encontrar o serviço **instância grande do SAP HANA**. À medida que escolhe esse serviço, pode selecionar tipos de problemas específicos, conforme mostrado:


![Selecione a classe problem no portal do Azure](./media/hana-li-portal/portal-select-problem-class.png)

Em cada um dos tipos diferentes de problema, é-lhe oferecida uma seleção de subtipos de problema, que tem de selecionar para caracterizar o seu problema ainda mais. Depois de selecionar o subtipo, agora pode atribuir o nome do requerente. Quando tiver terminado com o processo de seleção, pode mover para a próxima etapa da criação. Na **soluções** seção, está apontado para documentação de instâncias grandes do HANA, que pode gerar um ponteiro para uma solução do seu problema. Se não conseguir encontrar uma solução para o seu problema na documentação do sugeridas, vá para o passo seguinte. No próximo passo, vai ser-lhe pedido se o problema é com VMs ou com as unidades de instância grande do HANA. Isto ajuda a direcionar o pedido de suporte para os especialistas corretos. 

![Detalhes do incidente de suporte no portal do Azure](./media/hana-li-portal/portal-support-request-details.png)

Como respondeu às questões e fornecidos detalhes adicionais, pode aceder a próxima etapa para rever o pedido de suporte e envio-lo.

## <a name="next-steps"></a>Passos Seguintes

- [Como monitorizar o SAP HANA (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [Monitorizar e resolver problemas do lado do HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

