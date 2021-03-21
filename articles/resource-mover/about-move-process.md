---
title: Sobre o processo de mudança no Azure Resource Mover
description: Conheça o processo de movimentação de recursos em regiões com a Azure Resource Mover
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 02/01/2021
ms.author: raynew
ms.openlocfilehash: facbb30201aa6bde2044ca647383cc32ecd9ba26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980563"
---
# <a name="about-the-move-process"></a>Sobre o processo de mudança

[O Azure Resource Mover](overview.md) ajuda-o a mover recursos Azure através das regiões de Azure. Este artigo resume os componentes utilizados pelo Resource Mover e descreve o processo de movimento. 


## <a name="components"></a>Componentes

Estes componentes são utilizados durante o movimento da região.

**Componente** | **Detalhes**
--- | ---
**Mover de Recursos** |  A Resource Mover coordena com [os fornecedores de recursos da Azure](../azure-resource-manager/management/resource-providers-and-types.md) para orquestrar a movimentação de recursos entre regiões. A Resource Mover analisa as dependências de recursos e mantém e gere o estado dos recursos durante o processo de mudança. 
**Coleção move** |  Uma coleção de movimentos é um objeto [Azure Resource Manager.](../azure-resource-manager/management/overview.md)<br/><br/> A recolha de movimentos é criada durante o processo de movimento da região, para cada combinação emparelhada de regiões de origem e alvo numa subscrição. A recolha contém metadados e informações de configuração sobre os recursos que pretende mover.<br/><br/>Os recursos adicionados a uma recolha de movimentos devem estar na mesma subscrição, mas podem estar em diferentes grupos de recursos. 
**Mover recurso** | Quando adiciona um recurso a uma coleção de movimentos, é rastreado pela Resource Mover como um recurso de movimento.<br/><br/> A Resource Mover mantém informação para todos os recursos de movimento na recolha do movimento, e mantém uma relação de um para um entre a fonte e o recurso alvo. 
**Dependências** | A Resource Mover valida os recursos que adiciona a uma coleção e verifica se os recursos têm alguma dependência que não esteja na recolha de movimentos.<br/><br/> Depois de identificar dependências de um recurso, pode adicioná-las dependências à recolha de movimentos e movê-las também, ou pode selecionar recursos alternativos existentes na região alvo. Todas as dependências têm de ser resolvidas antes de começares a mudança. 



## <a name="move-region-process"></a>Mover o processo da região 

![Diagrama mostrando os passos de movimento](./media/about-move-process/move-steps.png)

Cada recurso de movimento passa pelos passos resumidos.

**Passo** | **Detalhes** | **Estado/Questões**
--- | --- | --- 
**Passo 1: Selecione recursos** | Selecione um recurso. O recurso é adicionado à coleção de movimentos. | Estado de recursos *move-se* para Preparar pendente .<br/><br/> Se o recurso tiver dependências, *validar a dependência* indica que é necessário adicionar recursos dependentes à recolha do movimento.
**Passo 2: Validar dependências** | Inicie o processo de validação.<br/><br/> Se a validação mostrar que os recursos dependentes estão pendentes, adicione-os à recolha de movimentos. <br/><br/> Adicione todos os recursos dependentes, mesmo que não queira movê-los. Mais tarde, pode especificar que os recursos que está a mover devem utilizar recursos diferentes na região alvo.<br/><br/> Valida-se de novo, até não haver dependências pendentes. | Depois de todas as dependências serem adicionadas e a validação for bem sucedida, o estado de recursos passa a *preparar-se pendente,* sem quaisquer problemas.
**Passo 3: Preparar** | Inicie o processo de preparação. Os passos de preparação dependem dos recursos que está a mover:<br/><br/> - **Recursos apátridas**: Os recursos apátridas têm apenas informação de configuração. Estes recursos não precisam de uma replicação contínua de dados para os mover. Exemplos incluem redes virtuais Azure (VNets), adaptadores de rede, equilibradores de carga e grupos de segurança de rede. Para este tipo de recurso, o processo De preparação gera um modelo de Gestor de Recursos Azure.<br/><br/> - **Recursos estatais**: Os recursos estatais têm informações de configuração e dados que precisam de ser movidos. Exemplos incluem VMs Azure e bases de dados Azure SQL. O processo de preparação difere para cada recurso. Pode incluir a replicação do recurso de origem para a região alvo. | Iniciar movimentos de recursos estado para *preparar em curso*.<br/><br/> Depois de preparar os acabamentos, o estado de recursos move-se para *Iniciar movimento pendente,* sem problemas.<br/><br/> Um processo mal sucedido move estado para *preparar falhou*.
**Passo 4: Iniciar movimento** | Inicie o processo de mudança. O método de movimento depende do tipo de recurso:<br/><br/> - **Apátrida**: Normalmente, para os recursos apátridas, o processo de movimento implementa um modelo importado na região alvo. O modelo baseia-se nas definições de recursos de origem e em quaisquer edições manuais que faça para definir o alvo.<br/><br/> - **Imponente**: Para recursos estatais, o processo de movimento pode implicar a criação do recurso, ou permitir uma cópia, na região alvo.<br/><br/>  Apenas para recursos estatais, iniciar uma mudança pode resultar em tempo de inatividade dos recursos de origem. Por exemplo, VMs e SQL. | O início do movimento desloca o Estado para *iniciar a mudança em curso.*<br/><br/> Um movimento iniciado com sucesso move o estado de recursos para *cometer movimento pendente,* sem problemas. <br/><br/> Um processo de movimento mal sucedido move o estado para *iniciar movimento falhou*.
**Passo 5 Opção 1: Movimento de devoluções** | Depois do movimento inicial, pode decidir se quer avançar com um movimento completo. Se não o fizer, pode descartar o movimento e o Resource Mover elimina os recursos criados no alvo. O processo de replicação de recursos estatais continua após o processo de devoluções. Esta opção é útil para testes. | Descartar recursos move estado para *descartar em curso*.<br/><br/> Devoluções bem sucedidas move estado para *Iniciar movimento pendente*, sem problemas.<br/><br/> Uma devoluções falhadas move estado para *descartar movimento falhou*. 
**Passo 5 Opção 2: Cometer movimento** | Depois do movimento inicial, se quiser avançar com um movimento completo, verifique os recursos na região alvo, e quando estiver pronto, cometa a mudança.<br/><br/> Apenas para recursos estatais, o compromisso pode resultar em recursos de origem como VMs ou SQL tornando-se inacessíveis. | Se cometer o movimento, o estado de recurso move-se para *Cometer movimento em progresso**.<br/><br/> Após um compromisso bem sucedido, o estado de recursos mostra *o movimento Commit concluído,* sem problemas.<br/><br/> Um compromisso falhado move estado para *cometer movimento falhou*.
**Passo 6: Eliminar fonte** | Depois de cometer o movimento e verificar os recursos na região alvo, pode eliminar o recurso de origem. | Após a sua commissão, um estado de recursos desloca-se para *eliminar a fonte pendente*. Em seguida, pode selecionar o recurso de origem e eliminá-lo.<br/><br/> - Apenas os recursos na *fonte de exclusão do* estado pendente podem ser eliminados. | A eliminação de um grupo de recursos ou do SQL Server no portal Resource Mover não é suportada. Estes recursos só podem ser eliminados da página de propriedades de recursos.


## <a name="move-region-states"></a>Move estados da região

O processo de mudança tem uma série de estados, e questões que podem surgir durante cada estado. Estes são resumidos no fluxograma.

![Fluxograma para possíveis estados e questões](./media/about-move-process/process.png)


### <a name="dependency-analysis"></a>Análise de dependência

À medida que progride através do processo de mudança, poderá ser-lhe pedido que valide as dependências se:
- Um recurso usa recursos dependentes que não estão na recolha de movimentos.
- Um recurso dependente na coleção de movimentos tem as suas próprias dependências que não estão na coleção de movimentos.
- Modificou as definições do alvo para o recurso e precisa de revalidar as dependências.


### <a name="remove-resources"></a>Remover recursos

Se não quiser mover um recurso, pode removê-lo da coleção de movimentos. Em geral, o recurso é então eliminado da coleção, juntamente com quaisquer ações ou objetos associados, tais como replicação, ou modelos armazenados. Exatamente o que acontece quando remove um recurso depende do tipo de recurso e do estado do recurso quando o apaga. [Saiba mais](remove-move-resources.md).

## <a name="move-impact"></a>Impacto do movimento

A tabela resume o impacto quando se atravessa regiões.

**Comportamento** | **Através de regiões**
--- | --- | --- 
**Dados** | Os dados de recursos e metadados são movidos.<br/><br/> Os metadados são armazenados temporariamente para rastrear o estado das dependências e operações dos recursos.
**Recurso** | O recurso de origem permanece intacto para garantir que as aplicações continuam a funcionar, e pode ser removido opcionalmente após a mudança.<br/><br/> Um recurso é criado na região alvo.
**Processo de mudança** | Processo em várias etapas que requer intervenção e monitorização manual.
**Teste** | O teste do movimento é importante, uma vez que as aplicações devem continuar a funcionar como esperado na região alvo, após a mudança.
**Tempo de inatividade** |  Não se espera perda de dados, mas algum tempo de inatividade para mover recursos.



## <a name="next-steps"></a>Passos seguintes

[Mover-se](tutorial-move-region-virtual-machines.md) VMs Azure para outra região.
[Mover-se](tutorial-move-region-sql.md) Recursos Azure SQL para outra região.