---
title: Gerir a infraestrutura de armazenamento para o Azure Stack | Documentos da Microsoft
description: Gerir a infraestrutura de armazenamento para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: ''
ms.date: 02/22/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.openlocfilehash: e843e09bf3a70f6deaa406642e33acc88f2b0189
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808596"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Gerir a infraestrutura de armazenamento para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo descreve o estado de funcionamento e o estado operacional dos recursos de infraestrutura de armazenamento do Azure Stack. Esses recursos incluem unidades de armazenamento e volumes. As informações neste tópico podem ser inestimáveis quando tentar resolver vários problemas, como uma unidade não pode ser adicionada a um conjunto.

## <a name="understand-drives-and-volumes"></a>Compreender as unidades e volumes

### <a name="drives"></a>Unidades

O Azure Stack, o software Windows Server definido capacidades de armazenamento, com uma combinação de espaços de armazenamento direto (S2D) e o Clustering de ativação pós-falha do servidor de Windows, para proporcionar um desempenho elevado, o serviço de armazenamento dimensionáveis e resilientes.

Parceiros de sistema do Azure Stack integrado oferecem inúmeras variações de solução, incluindo uma vasta gama de flexibilidade de armazenamento. Atualmente pode selecionar uma combinação de três tipos de unidade: NVMe (memória não volátil Express), SATA/SSD (unidade de estado sólido), de SAS HDD (unidade de disco rígido).

Espaços de armazenamento direto, apresenta uma cache para maximizar o desempenho de armazenamento. Na aplicação do Azure Stack com único ou vários tipos de unidades, espaços de armazenamento direto utilizam automaticamente todas as unidades do "mais rápida" (NVMe &gt; SSD &gt; HDD) tipo para a colocação em cache. As restantes unidades são utilizadas para capacidade. As unidades podem ser agrupadas para implementação de um "tudo flash" ou "híbrido":

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image1.png)

Tudo flash objetivo de implementações para maximizar o desempenho de armazenamento e não incluem unidades de disco rígido rotação (HDD).

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image2.png)

Implementações híbridas têm como objetivo equilibrar o desempenho e a capacidade ou para maximizar a capacidade e incluem unidades de disco rígido rotação (HDD).

O comportamento da cache é determinado automaticamente com base nos tipos de unidades de que estão a ser armazenada em cache para. Quando a colocação em cache para unidades de estado sólido (por exemplo, NVMe colocação em cache para os SSDs), apenas escritas são colocadas em cache. Isso reduz o desgaste pode eventualmente nas unidades de capacidade, reduzindo o tráfego cumulativo para as unidades de capacidade e expandir a sua vida útil. Entretanto, como leituras afeta significativamente o tempo de vida de flash, e unidades de estado sólido universalmente oferecem baixa latência de leitura, leituras não estão em cache. Quando a colocação em cache para unidades de disco rígido (por exemplo, o SSDs colocação em cache para HDDs), as leituras e gravações são colocadas em cache, para fornecer uma latência semelhante ao flash (muitas vezes, ~ 10 vezes melhor) para ambos.

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image3.png)

Para a configuração disponível de armazenamento, pode verificar o parceiro do Azure Stack OEM (https://azure.microsoft.com/overview/azure-stack/partners/) para especificação detalhada.

> [!Note]  
> Aplicação do Azure Stack pode ser fornecida numa implementação híbrida, com as unidades de tanto HDD e SSD (ou NVMe). Mas as unidades do tipo mais rápido seriam usadas como unidades de cache e todas as restantes unidades seriam usadas como unidades de capacidade que um conjunto. Os dados de inquilino (blobs, tabelas, filas e discos) seriam colocados em unidades de capacidade. Então, os discos premium de aprovisionamento ou selecionar a conta de armazenamento premium tipo não significa que os objetos são garantidas a ser alocada em SSD ou NVMe unidades e obter o melhor desempenho.

### <a name="volumes"></a>Volumes

O *serviço de armazenamento* particiona o armazenamento disponível em volumes separados que são alocados para armazenar dados de sistema e de inquilino. Volumes combinam as unidades no agrupamento de armazenamento para apresentar os benefícios de desempenho de espaços de armazenamento direto, a escalabilidade e a tolerância a falhas.

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image4.png)

Existem três tipos de volumes criados num agrupamento de armazenamento do Azure Stack:

-   Infraestrutura: arquivos de host utilizados por VMs de infraestrutura do Azure Stack e serviços principais.

-   Temp VM: host os discos temporários anexados a VMs de inquilino e os dados estão armazenados nestes discos.

-   Objeto Store: dados de inquilino do host blobs, tabelas, filas e discos de VM de manutenção.

Numa implementação de vários nó, verá três volumes de infraestrutura, enquanto o número de volumes de Temp de VM e os volumes de objeto Store é igual ao número de nós na implementação do Azure Stack:

-   Numa implementação de quatro nós, existem quatro volumes Temp de VM igual e quatro volumes de objeto Store igual.

-   Se adicionar um novo nó ao cluster, haveria um novo volume para ambos os tipos de ser criado.

-   O número de volumes permanece a mesma, mesmo que um nó a funcionar incorretamente ou é removido.

-   Se utilizar o Azure Stack Development Kit, há um único volume com múltiplas partilhas.

Volumes em espaços de armazenamento direto proporcionam resiliência contra problemas de hardware, como falhas de servidor ou de unidade e para ativar a disponibilidade contínua em toda a manutenção de servidores, tais como atualizações de software. Implementação de pilha do Azure está a utilizar o espelhamento de três vias para garantir a resiliência de dados. Três cópias dos dados de inquilino são escritas em diferentes servidores, onde eles chegam em cache:

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image5.png)

O espelhamento fornece tolerância a falhas ao manter várias cópias de todos os dados. Como esses dados são distribuídos e colocados não são simples (consulte este blogue para saber mais), mas é absolutamente verdadeiro para dizer que todos os dados armazenados com o espelhamento são escritos, em sua totalidade, várias vezes. Cada cópia é escrita para físicas diferentes hardware (unidades diferentes em diferentes servidores) que devem para falhar de forma independente. Espelhamento de três vias pode tolerar com segurança, pelo menos, dois problemas de hardware (unidade ou servidor) ao mesmo tempo. Por exemplo, se estiver a reiniciar um servidor quando, de repente, outro servidor ou de unidade falhar, todos os dados permanecem seguros e continuamente acessível.

## <a name="volume-states"></a>Estados de volume

Para saber quais são volumess de estado no, utilize os seguintes comandos do PowerShell:

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

Get-AzsVolume - ScaleUnit \$scaleunit\_nomeie - StorageSubSystem \$subsistema\_nome | Select-Object VolumeLabel, HealthStatus, operationalstatus é apresentado, RepairStatus, descrição, ação, TotalCapacityGB, RemainingCapacityGB

Eis um exemplo de saída que mostra um volume desligado e um volume degradado/incompleta:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Desconhecidos | Desligado |
| ObjStore_2 | Aviso | {Degradado, incompleta} |

As secções seguintes apresentam o estado de funcionamento e os Estados operacionais.

### <a name="volume-health-state-healthy"></a>Estado de funcionamento do volume: Bom estado de funcionamento

| Estado operacional | Descrição |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | O volume está em bom estado. |
| Inferior ao ideal | Dados não for escritos uniformemente por unidades.<br> <br>**Ação:** Contacte o suporte para otimizar a utilização de disco no agrupamento de armazenamento. Antes de fazer, iniciar o processo de recolha de ficheiros de registo através da orientação do https://aka.ms/azurestacklogfiles. Poderá ter de restaurar a partir de cópia de segurança após a falha de ligação é restaurada. |


### <a name="volume-health-state-warning"></a>Estado de funcionamento do volume: Aviso

Quando o volume está num Estado de funcionamento de aviso, significa que um ou mais cópias dos seus dados não estão disponíveis, mas o Azure Stack pode continuar a ler a, pelo menos, uma cópia dos seus dados.

| Estado operacional | Descrição |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| No serviço | O Azure Stack é a reparar o volume, tal como após a adição ou remoção de uma unidade. Quando o reparo estiver concluído, deverá devolver o volume para o estado de funcionamento OK.<br> <br>**Ação:** Aguarde para o Azure Stack concluir o volume a reparar e verificar o estado mais tarde. |
| Incompleto | A resiliência do volume é reduzida porque uma ou mais unidades de falharam ou estão em falta. No entanto, as unidades em falta contêm cópias atualizadas dos seus dados.<br> <br>**Ação:** Voltar a ligar todas as unidades em falta, substitua todas as unidades com falha e colocar online todos os servidores que estão offline. |
| Degradado | A resiliência do volume é reduzida porque uma ou mais unidades falhou ou estão em falta e há Desatualizadas cópias dos seus dados destas unidades.<br> <br>**Ação:** Voltar a ligar todas as unidades em falta, substitua todas as unidades com falha e colocar online todos os servidores que estão offline. |

 

### <a name="volume-health-state-unhealthy"></a>Estado de funcionamento do volume: Estado de funcionamento incorreto

Quando um volume está num Estado de funcionamento mau estado de funcionamento, alguns ou todos os dados no volume não está atualmente acessíveis.

| Estado operacional | Descrição |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nenhuma redundância | O volume perdeu dados porque falhou demasiadas unidades.<br> <br>**Ação:** Contacte o suporte. Antes de fazer, iniciar o processo de recolha de ficheiros de registo através da orientação do https://aka.ms/azurestacklogfiles. |


### <a name="volume-health-state-unknown"></a>Estado de funcionamento do volume: Desconhecidos

O volume também pode ser no Estado desconhecido do Estado de funcionamento se o disco virtual tem de se tornar desanexado.

| Estado operacional | Descrição |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Desligado | Ocorreu uma falha de dispositivo de armazenamento um que pode fazer com que o volume ficará inacessível. Alguns dados poderão perder-se.<br> <br>**Ação:** <br>1. Verifique o físico e conectividade de todos os dispositivos de armazenamento de rede.<br>2. Se todos os dispositivos estão ligados corretamente, contacte o suporte. Antes de fazer, iniciar o processo de recolha de ficheiros de registo através da orientação do https://aka.ms/azurestacklogfiles. Poderá ter de restaurar a partir de cópia de segurança após a falha de ligação é restaurada. |

## <a name="drive-states"></a>Estados de unidade

Utilize os seguintes comandos do PowerShell para monitorizar o estado de unidades:

 

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

, SerialNumber

 

Get-AzsDrive - ScaleUnit \$scaleunit\_nomeie - StorageSubSystem \$subsistema\_nome | Select-Object StorageNode, PhysicalLocation, HealthStatus, operationalstatus é apresentado, descrição, ação, utilização, CanPool, CannotPoolReason, SerialNumber, modelo, MediaType, CapacityGB

As secções seguintes descrevem os Estados de funcionamento pode ter uma unidade.

### <a name="drive-health-state-healthy"></a>Unidade de estado de funcionamento: Bom estado de funcionamento

| Estado operacional | Descrição |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | O volume está em bom estado. |
| No serviço | A unidade está a efetuar algumas operações de manutenção interna. Quando a ação for concluída, deverá devolver a unidade para o estado de funcionamento OK. |

### <a name="drive-health-state-healthy"></a>Unidade de estado de funcionamento: Bom estado de funcionamento

Uma unidade na lata de estado de aviso ler e escrever dados com êxito, mas tem um problema.

| Estado operacional | Descrição |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Comunicação perdida | Conectividade foi perdida na unidade.<br> <br>**Ação:** Coloque todos os servidores novamente online. Se de que não corrigi-lo, voltar a ligar a unidade. Se isto mantém a acontecer, substitua a unidade para garantir a resiliência completa. |
| Falha preditiva | Uma falha da unidade é prevista para ocorrer em breve.<br> <br>**Ação:** Substitua a unidade logo que possível para garantir a resiliência completa. |
| Erro de e/s | Ocorreu um erro temporário acessar a unidade.<br> <br>**Ação:** Se isto mantém a acontecer, substitua a unidade para garantir a resiliência completa. |
| Erro transitório | Ocorreu um erro temporário com a unidade. Isso normalmente significa a unidade foi não responde, mas pode também significar que os espaços de armazenamento direto partição proteção inadequadamente foi removida do disco. <br> <br>**Ação:** Se isto mantém a acontecer, substitua a unidade para garantir a resiliência completa. |
| Latência anormal | A unidade, por vezes, está a responder e está a mostrar sinais de falha.<br> <br>**Ação:** Se isto mantém a acontecer, substitua a unidade para garantir a resiliência completa. |
| A remover do conjunto | O Azure Stack está no processo de remover a unidade do seu pool de armazenamento.<br> <br>**Ação:** Aguarde para o Azure Stack acabar de remover a unidade e verificar o estado mais tarde.<br>Se o estado permanece, contacte o suporte. Antes de fazer, iniciar o processo de recolha de ficheiros de registo através da orientação do https://aka.ms/azurestacklogfiles. |
|  |  |
| Modo de manutenção inicial | O Azure Stack está no processo de colocar a unidade no modo de manutenção. Este é um estado temporário – a unidade deve estar disponível em breve o estado de modo de manutenção do In.<br> <br>**Ação:** Aguarde para o Azure Stack concluir o processo e verificar o estado mais tarde. |
| No modo de manutenção | A unidade está no modo de manutenção, interrompendo a leituras e escritas da unidade. Isso normalmente significa a tarefas de administração do Azure Stack como PNU ou FRU está a funcionar a unidade. Mas o administrador também pode colocar a unidade no modo de manutenção.<br> <br>**Ação:** Aguarde para o Azure Stack concluir a tarefa de administração e verificar o estado mais tarde.<br>Se o estado permanece, contacte o suporte. Antes de fazer, iniciar o processo de recolha de ficheiros de registo através da orientação do https://aka.ms/azurestacklogfiles. |
|  |  |
| A parar o modo de manutenção | O Azure Stack está no processo de colocar online a unidade. Este é um estado temporário – a unidade deve estar disponível em breve outro Estado - o ideal é que em bom estada.<br> <br>**Ação:** Aguarde para o Azure Stack concluir o processo e verificar o estado mais tarde. |

 

### <a name="drive-health-state-unhealthy"></a>Unidade de estado de funcionamento: Estado de funcionamento incorreto

Uma unidade de estado de funcionamento incorreto atualmente não pode ser escrita ou acessada.

| Estado operacional | Descrição |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dividir | A unidade tem de tornar-se separados do conjunto.<br> <br>**Ação:** Substitua a unidade com um novo disco. Se tiver de utilizar este disco, remover o disco do sistema, certificar-se de que não há nenhum dado útil no disco, apagar o disco e, em seguida, reseat o disco. |
| Não pode ser utilizada | O disco físico foi colocado em quarentena porque não é suportado pelo seu fornecedor de solução. São suportados apenas os discos que são aprovados para a solução e dispõem do firmware de disco correta.<br> <br>**Ação:** Substitua a unidade com um disco que tenha um fabricante aprovado e o número de modelo para a solução. |
| Metadados obsoletos | O disco de substituição foi utilizado anteriormente e pode conter dados a partir de um sistema de armazenamento desconhecido. O disco foi colocado em quarentena.        <br> <br>**Ação:** Substitua a unidade com um novo disco. Se tiver de utilizar este disco, remover o disco do sistema, certificar-se de que não há nenhum dado útil no disco, apagar o disco e, em seguida, reseat o disco. |
| Metadados não reconhecidos | Metadados não reconhecidos encontrado na unidade, que geralmente significa que a unidade tem metadados de um agrupamento diferente no mesmo.<br> <br>**Ação:** Substitua a unidade com um novo disco. Se tiver de utilizar este disco, remover o disco do sistema, certificar-se de que não há nenhum dado útil no disco, apagar o disco e, em seguida, reseat o disco. |
| Mídia com falha | A unidade de falha e não será utilizada pelos espaços de armazenamento mais.<br> <br>**Ação:** Substitua a unidade logo que possível para garantir a resiliência completa. |
| Falha de hardware do dispositivo | Ocorreu uma falha de hardware nesta unidade. <br> <br>**Ação:** Substitua a unidade logo que possível para garantir a resiliência completa. |
| Atualizar o firmware | O Azure Stack está a atualizar o firmware da unidade. Este é um estado temporário que, normalmente, dura menos de um minuto e durante o qual o tempo outras unidades no agrupamento de lidar com todas as leituras e escritas.<br> <br>**Ação:** Aguarde para o Azure Stack concluir a atualização e verificar o estado mais tarde. |
| A iniciar | A unidade está a preparar para a operação. Deve ser um estado temporário - quando tiver terminado, que a unidade deve fazer a transição para um Estado operacional diferente.<br> <br>**Ação:** Aguarde para o Azure Stack concluir a operação e verifique o estado mais tarde. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Motivos de que uma unidade não pode ser agrupada

Algumas unidades apenas não estiverem prontas para ser no agrupamento de armazenamento do Azure Stack. Pode descobrir por que uma unidade não é elegível para agrupamento examinando a propriedade CannotPoolReason de uma unidade. A tabela seguinte fornece mais detalhes sobre cada um dos motivos pelos quais.

| Razão | Descrição |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hardware em não conformidade | A unidade não está na lista de modelos de armazenamento aprovados especificado com o serviço de estado de funcionamento.<br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Firmware em não conformidade | Não é o firmware da unidade física na lista de revisões de firmware aprovados com o serviço de estado de funcionamento.<br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Em utilização pelo cluster | A unidade é atualmente utilizada por um Cluster de ativação pós-falha.<br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Suporte de dados amovível | A unidade é classificada como uma unidade removível. <br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Estado de funcionamento incorreto | A unidade não está em bom estado de funcionamento e poderá ter de ser substituído.<br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Capacidade insuficiente | Existem partições ocupando o espaço livre na unidade.<br> <br>**Ação:** Substitua a unidade com um novo disco. Se tiver de utilizar este disco, remover o disco do sistema, certificar-se de que não há nenhum dado útil no disco, apagar o disco e, em seguida, reseat o disco. |
| Verificação em curso | O serviço de integridade está a verificar para ver se o firmware da unidade ou unidade foi aprovada para utilização.<br> <br>**Ação:** Aguarde para o Azure Stack concluir o processo e verificar o estado mais tarde. |
| A verificação falhou | O serviço de estado de funcionamento não foi possível verificar se a unidade ou o firmware da unidade é aprovada para utilização.<br> <br>**Ação:** Contacte o suporte. Antes de fazer, iniciar o processo de recolha de ficheiros de registo através da orientação do https://aka.ms/azurestacklogfiles. |
| Offline | A unidade está offline. <br> <br>**Ação:** Contacte o suporte. Antes de fazer, iniciar o processo de recolha de ficheiros de registo através da orientação do https://aka.ms/azurestacklogfiles. |

## <a name="next-step"></a>Passo seguinte

[Gerir a capacidade de armazenamento](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-storage-shares) 