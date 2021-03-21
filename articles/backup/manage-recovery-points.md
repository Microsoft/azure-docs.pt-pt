---
title: Gerir pontos de recuperação
description: Saiba como o serviço Azure Backup gere pontos de recuperação para máquinas virtuais
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94428709"
---
# <a name="manage-recovery-points"></a>Gerir pontos de recuperação

Este artigo descreve como a retenção funciona para máquinas virtuais. Sempre que acontecem cópias de segurança, são criados pontos de recuperação a partir dos quais as operações de restauro podem ser realizadas.

Para máquinas virtuais, a cópia de segurança inicial é uma cópia de segurança completa e as cópias de segurança subsequentes são cópias de segurança incrementais.

## <a name="recovery-points-and-retention"></a>Pontos de recuperação e retenção

### <a name="scheduled-initial-and-incremental-backup"></a>Backup inicial e incremental programado

Tomemos um exemplo simplificado da máquina virtual *V1* com um disco de dados composto por quatro blocos: Bloco 1, Bloco 2, Bloco 3 e Bloco 4. Cada bloco tem 16 KB de tamanho.

![Máquina virtual com quatro blocos](./media/manage-recovery-points/four-blocks.png)

**Passo 1 - Cópia de segurança inicial:** O reforço inicial é um reforço completo. Atua como uma linha de base na qual são aplicadas cópias de segurança incrementais subsequentes. Suponha que há dados escritos no Bloco 1 e no Bloco 2 na fonte VM. Os mesmos dados serão replicados como D1 e D2 no armazenamento do cofre dos Serviços de Recuperação.

![A cópia de segurança inicial é replicada](./media/manage-recovery-points/initial-backup.png)

**Passo 2 - Apoio incremental 1:** Considere que há novos dados adicionados ao bloco 3 do VM. Os mesmos dados serão replicados na próxima cópia de segurança incremental e apenas o bloco que é alterado é armazenado como D3.  Durante cada passo, mesmo que 1 KB do bloco mude, todo o bloco de 16 KB é carregado no ponto de recuperação.

![Primeira cópia de segurança incremental](./media/manage-recovery-points/first-incremental-backup.png)

**Passo 3 - Apoio incremental 2:**  Agora considere que há alterações de dados no bloco 3 e bloco 2 na fonte VM. Estas alterações serão replicadas na próxima cópia de segurança incremental como D3' e D2'.

![Segunda cópia de segurança incremental](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Backup a pedido

Pode optar por executar uma cópia de segurança a pedido de um VM a qualquer momento depois de configurar proteção sobre o mesmo.

- O backup a pedido será um backup completo se for acionado antes do primeiro backup inicial programado.
- Se a cópia de segurança inicial estiver completa e uma cópia de segurança a pedido for ativada, então é uma cópia de segurança incremental.
- O tempo de retenção dos pontos de recuperação criados para uma cópia de segurança a pedido é o valor de retenção que especifica quando ativa a cópia de segurança.

### <a name="storage-cost"></a>Custo do armazenamento

O **ponto de recuperação** criado para a cópia de segurança inicial contém todos os blocos que têm os dados. Os pontos de recuperação incrementais subsequentes consistem apenas nos blocos que alteraram os dados. Os custos de armazenamento correspondem à soma de todos os blocos que se estendem por todos os pontos de recuperação.

Vamos usar o exemplo acima para entender o custo de armazenamento após cada passo:

|Passo  |Tipo de cópia de segurança  |Os blocos mudaram  |Tipo de armazenamento |
|------|---------|---------|---------|
|1     |     Cópia de segurança inicial    | Bloco 1, Bloco 2        |    Correspondente ao ponto de recuperação 1(D1+D2)     |
|2     |  Backup Incremental 1       |  Bloco 3       |   Correspondente ao ponto de recuperação 1(D1+D2) + ponto de recuperação 2(D3)      |
|3     |    Backup Incremental 2     |    Bloco 2, Bloco 3     |   Correspondente ao ponto de recuperação 1(D1+D2) + ponto de recuperação 2(D3) + ponto de recuperação 3(D2'+D3')      |

### <a name="recovery-point-expiration"></a>Expiração do ponto de recuperação

Cada ponto de recuperação tem uma duração de retenção, conforme especificado na política de backup. A limpeza acontece em intervalos regulares e todos os pontos de recuperação que tenham expirado são limpos.

Quando o ponto de recuperação expira, ou é apagado ou fundido.

### <a name="case-1-initial-recovery-point-expires"></a>Caso 1: O ponto de recuperação inicial expira

Quando o ponto de recuperação inicial expira, funde-se com o próximo ponto de recuperação incremental. Todos os blocos de dados que são substituídos no ponto de recuperação incremental são eliminados e os restantes são fundidos. A cópia de segurança incremental torna-se então a cópia de segurança inicial. Vamos rever com um exemplo:

- *O Ponto de Recuperação 1* que é criado durante a cópia de segurança inicial tem a cópia de segurança completa do VM.
- Quando *o Ponto de Recuperação 1* expirar, o Ponto de *Recuperação 2* é o próximo reforço completo.
- O Bloco D1 é fundido com *o Ponto de Recuperação 2* e o D2 é eliminado uma vez que os dados do bloco 2 são substituídos no *Ponto de Recuperação 2*. Esta mudança é capturada como bloco D2'.
- O Bloco D1 é mantido nos pontos de recuperação consecutivos como está, até que haja alterações antes do próximo backup.

![Primeiro caso](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>Caso 2: Entre o ponto de recuperação incremental expira

- Se *o Ponto de Recuperação 2* expirar antes do ponto de *recuperação 1,* os dados do Ponto de *Recuperação 2* são fundidos com o próximo ponto de recuperação disponível: *Ponto de recuperação 3*. Assim, o bloco D3 é fundido com *o Ponto de Recuperação 3*.
- *O Ponto de Recuperação 1* ainda é o reforço completo com os blocos D1 e D2.

![Segundo caso](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>Caso 3: Ponto de recuperação a pedido expira

Neste exemplo, está prevista uma política de horário (backup diário) com período de retenção *de n* dias.  Se um backup a pedido for acionado no quarto dia antes do próximo backup programado e o seu período de retenção for especificado como 10 dias, então continuará a ser um backup incremental. Um ponto de recuperação *(On-demand RP1*) será criado após *o Ponto de Recuperação 3* e antes *do Ponto de Recuperação 4*.  No final do dia 14, o ponto de recuperação a pedido (*On-demand RP1*) expira, e será fundido com o próximo ponto de recuperação disponível. Os blocos de dados que ainda estão presentes no servidor são fundidos, enquanto os blocos de dados que foram alterados (substituídos ou eliminados) são eliminados do ponto de recuperação expirado.

![Terceiro caso](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>Impacto da mudança de política nos pontos de recuperação

Quando uma política é modificada, é aplicada a pontos de recuperação novos e existentes. Para obter mais informações, consulte [o impacto da mudança de política nos pontos de recuperação.](backup-architecture.md#impact-of-policy-change-on-recovery-points)

### <a name="impact-of-stop-protection-on-recovery-points"></a>Impacto da proteção de paragem nos pontos de recuperação

Há duas maneiras de parar de proteger um VM:

- **Pare a proteção e elimine os dados de backup.** Esta opção impedirá que todos os futuros trabalhos de backup protejam o seu VM e elimina todos os pontos de recuperação. Se [a eliminação suave](backup-azure-security-feature-cloud.md) estiver ativada, os dados eliminados serão conservados durante 14 dias. As taxas não são incorridos para itens em estado de apagação suave. Os dados podem não ser desesdilatados no prazo de 14 dias. Se a eliminação suave não estiver ativada, os dados serão imediatamente limpos e não poderá restaurar o VM ou utilizar a opção **de backup do Currículo.**
- **Pare a proteção e retenha os dados de backup.** Esta opção impedirá que todos os futuros trabalhos de backup protejam o seu VM. No entanto, o serviço Azure Backup irá manter para sempre os pontos de recuperação que foram apoiados. Terá de pagar para manter os pontos de recuperação no cofre (ver [preços de reserva do Azure](https://azure.microsoft.com/pricing/details/backup/) para mais detalhes). Poderá restaurar o VM, se necessário. Se decidir retomar a proteção VM, poderá utilizar a opção **de backup do Currículo.** Após o reinício da cópia de segurança, as regras de retenção serão aplicadas aos pontos de validade. Também pode eliminar os dados de backup utilizando a opção **de dados de backup Eliminar.**

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>Impacto da eliminação de um VM sem proteção para parar

A eliminação de um VM sem proteção stop tem impacto nos pontos de recuperação, e é um cenário indesejável. Idealmente, as cópias de segurança devem ser interrompidas antes de eliminar a máquina virtual. Uma vez que o recurso não existe, as cópias de segurança programadas falharão com o [erro VMNotFoundV2](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). Os pontos de recuperação serão limpos periodicamente de acordo com a política de retenção, mas a última cópia da máquina virtual permanecerá para sempre e será faturada em conformidade. Dependendo do seu cenário, tem as seguintes duas opções:

- **Opção 1:** Restaurar o VM utilizando qualquer um dos pontos de recuperação. Se pretender recuperar o VM apagado, restaure com o mesmo nome e no mesmo grupo de recursos. Se proteger o VM restaurado no mesmo cofre, os pontos de recuperação existentes serão automaticamente ligados.
- **Opção 2:** Vá ao cofre dos Serviços de Recuperação e pare a proteção com os dados de eliminação.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>Impacto dos pontos de recuperação expirados para itens em estado de apagação suave

Se [a eliminação suave](backup-azure-security-feature-cloud.md) estiver ativada para o cofre dos serviços de recuperação, o ponto de recuperação expirado permanece em estado de eliminação suave e não é limpo. Não são incorridos encargos quando um ponto de recuperação está em estado de apagação suave.

### <a name="impact-of-churn-on-backup-performance"></a>Impacto do impacto no desempenho de backup

Suponha que o armazenamento total de um VM é de 8 TB e o churn é de 5%. Em seguida, o armazenamento de backup incremental correspondente será de 5% de 8 TB que é 0,4 TB. O churn mais alto corresponde a um armazenamento de backend mais elevado para cópias de segurança incrementais subsequentes. O churn impacta o desempenho de reserva. Maior a agitação, mais lento o processo de backup, e maior o consumo de armazenamento de backend.

Para entender como o churn impacta o desempenho de backup, olhe para este cenário:

|Máquinas virtuais  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|Número de discos de dados    | 4 (A1, A2, A3, A4)        | 4(B1, B2, B3, B4)        |  4(C1, C2, C3, C4)       |
|Tamanho de cada disco   |      4 TB   | 4 TB        |  4 TB       |
|Churn de dados de backup    |   A1- 4 TB      | B1-1 TB; B2-1 TB <br> B3-1 TB; B4-1 TB  |   C1-2 TB; C4-2 TB      |

O desempenho de backup será na ordem VM2>VM3>VM1. A razão para isso é que os dados rebentados estão espalhados pelos vários discos. Uma vez que a cópia de segurança dos discos acontece em paralelo, o VM2 mostrará o melhor desempenho.

## <a name="next-steps"></a>Passos seguintes

- [Arquitetura e componentes Azure Backup](backup-architecture.md)
