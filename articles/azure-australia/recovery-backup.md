---
title: Backup e recuperação de desastre na Austrália do Azure
description: Backup e recuperação de desastre em Microsoft Azure para agências governamentais da Austrália, pois ele se relaciona com o ASD Essential 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571526"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Backup e recuperação de desastre na Austrália do Azure

Ter planos de backup e recuperação de desastres com a infraestrutura de suporte in-loco é essencial para todas as organizações. A importância de ter uma solução de backup é realçada por sua inclusão no [essencial 8 da central de segurança da Austrália](https://acsc.gov.au/publications/protect/essential-eight-explained.htm).

O Microsoft Azure fornece dois serviços que habilitam a resiliência: Backup e Azure Site Recovery do Azure. Esses serviços permitem que você proteja seus dados, tanto localmente quanto na nuvem, para uma variedade de cenários de design. O backup do Azure e o Azure Site Recovery usam um recurso comum de armazenamento e gerenciamento: o cofre dos serviços de recuperação do Azure. Esse cofre é usado para gerenciar, monitorar e separar os dados de backup e Azure Site Recovery do Azure.

Este artigo fornece detalhes sobre os principais elementos de design para implementar o backup do Azure e Azure Site Recovery em linha com os controles do ISM (manual de segurança de informações) dos [sinais australianos do Directorate (ASD)](https://acsc.gov.au/infosec/ism/index.htm).

## <a name="azure-backup"></a>Azure Backup

![Azure Backup](media/backup-overview.png)

O backup do Azure é semelhante a uma solução de backup local tradicional e fornece a capacidade de fazer backup de dados locais e hospedados no Azure. O backup do Azure pode ser usado para fazer backup dos seguintes tipos de dados no Azure:

* Ficheiros e pastas
* Sistemas operacionais Windows e Linux com suporte hospedados em:
  * Hipervisores Hyper-V e VMWare
  * Hardware físico
* Aplicativos Microsoft com suporte

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

O Azure Site Recovery Replica cargas de trabalho que consistem em uma única máquina virtual ou aplicativos de várias camadas. A replicação tem suporte do local para o Azure, entre regiões do Azure ou entre locais no local orquestradas por Azure Site Recovery. As máquinas virtuais locais podem ser replicadas no Azure ou em um hipervisor local com suporte. Uma vez configurado, o Azure Site Recovery orquestra a replicação, o failover e o failback.

## <a name="key-design-considerations"></a>Considerações de design-chave

Ao implementar uma solução de backup ou recuperação de desastres, a solução proposta precisa considerar:

* O escopo e o volume de dados a serem capturados
* Por quanto tempo os dados serão retidos
* Como esses dados são armazenados e gerenciados com segurança
* As localizações geográficas em que os dados são armazenados
* Procedimentos de teste de sistema de rotina

O ISM fornece orientação sobre as considerações de segurança que devem ser feitas durante a criação de uma solução. Microsoft Azure fornece meios para resolver essas considerações de segurança.

### <a name="data-sovereignty"></a>Soberania de dados

As organizações precisam garantir que a soberania de dados seja mantida quando os locais de armazenamento baseados em nuvem do utilising. Azure Policy fornece os meios para restringir os locais permitidos em que um recurso do Azure pode ser criado. O Azure Policy interno "locais permitidos" é usado para garantir que os recursos do Azure criados no escopo de um Azure Policy atribuído só possam ser criados nas localizações geográficas nomeadas.

Os itens de Azure Policy para a restrição geográfica dos recursos do Azure são:

* allowedLocations
* allowedSingleLocation

Essas políticas permitem que os administradores do Azure restrinjam a criação a uma lista de locais nomeados ou mesmo como um único local geográfico.

### <a name="redundant-and-geographically-dispersed-storage"></a>Armazenamento redundante e geograficamente disperso

Os dados armazenados no cofre do serviço de recuperação do Azure sempre são armazenados no armazenamento redundante. Por padrão, o cofre do serviço de recuperação usa o GRS (armazenamento com redundância geográfica do Azure). Os dados armazenados usando GRS são replicados para outros centers de dados do Azure na [região emparelhada secundária](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)do cofre do serviço de recuperação. Esses dados replicados são armazenados como somente leitura e só se tornam graváveis se houver um evento de failover do Azure. No data center do Azure, os dados são replicados entre domínios de falha e domínios de atualização separados para minimizar a chance de interrupção baseada em hardware ou manutenção. O GRS fornece pelo menos 99.99999999999999 de disponibilidade anual.

O cofre dos serviços de recuperação do Azure pode ser configurado para Utilise LRS (armazenamento com redundância local). O LRS é uma opção de armazenamento de menor custo com a compensação da disponibilidade reduzida. Esse modelo de redundância emprega a mesma replicação entre domínios de falha e domínios de atualização separados, mas não é replicado entre regiões geográficas. Os dados localizados no armazenamento LRS, embora não tão resiliente como GRS, ainda fornecem pelo menos 99,999999999% de disponibilidade anual.

Ao contrário das tecnologias tradicionais de armazenamento externo, como mídia de fita, as cópias adicionais dos dados são criadas automaticamente e não exigem nenhuma sobrecarga administrativa adicional.

### <a name="restricted-access-and-activity-monitoring"></a>Monitoramento de atividade e acesso restrito

Os dados de backup devem ser protegidos contra a corrupção, modificação e exclusão não aprovada. O backup do Azure e o Azure Site Recovery fazem uso da malha de gerenciamento do Azure comum. Essa malha fornece auditoria detalhada, registro em log e RBAC (controle de acesso baseado em função) para recursos localizados no Azure. O acesso aos dados de backup pode ser restrito para selecionar a equipe administrativa e todas as ações que envolvem dados de backup podem ser registradas em log e auditadas.

O backup do Azure e os Azure Site Recovery têm recursos internos de registro em log e relatórios. Todos os problemas que ocorrem durante o backup ou a replicação são relatados aos administradores usando a malha de gerenciamento do Azure.

O cofre dos serviços de recuperação do Azure também tem as seguintes medidas de segurança de dados adicionais em vigor:

* Os dados de backup são mantidos por 14 dias após a ocorrência de uma operação de exclusão
* Alertas e notificações para operações críticas, como "parar backup com excluir dados"
* Requisitos de PIN de segurança para operações críticas
* Verificações de intervalo de retenção mínimas estão em vigor

Essas verificações de intervalo de retenção mínimas incluem:

* Para retenção diária, um mínimo de sete dias de retenção
* Para a retenção semanal, um mínimo de quatro semanas de retenção
* Para retenção mensal, um mínimo de três meses de retenção
* Para retenção anual, um mínimo de um ano de retenção

Todos os dados de backup armazenados no Azure são criptografados em repouso usando o Criptografia do Serviço de Armazenamento do Azure (SSE). Isso é habilitado para todas as contas de armazenamento novas e existentes por padrão e não pode ser desabilitado. Os dados criptografados são descriptografados automaticamente durante a recuperação. Por padrão, os dados criptografados usando o SSE são criptografados usando uma chave fornecida pelo e gerenciados pela Microsoft. As organizações podem optar por fornecer e gerenciar sua própria chave de criptografia para uso com o SSE. Isso fornece uma camada adicional opcional de segurança para os dados criptografados. Essa chave pode ser armazenada pelo cliente no local ou com segurança no cofre de chaves do Azure.

### <a name="secure-data-transport"></a>Transporte de dados seguro

Dados de backup do Azure criptografados em trânsito usando o AES 256. Esses dados são protegidos por meio do uso de uma senha criada pela equipe administrativa quando o backup é configurado pela primeira vez. A Microsoft não tem acesso a essa senha, o que significa que o cliente deve garantir que essa senha seja armazenada com segurança. Em seguida, a transferência de dados ocorre entre o ambiente local e o cofre dos serviços de recuperação do Azure por meio de uma conexão HTTPS segura.  Os dados no cofre dos serviços de recuperação são criptografados em repouso usando o Azure SSE.

Azure Site Recovery dados também são sempre criptografados em trânsito. Todos os dados replicados são transportados com segurança para o Azure usando HTTPS e TLS. Quando um cliente do Azure se conecta ao Azure usando uma conexão de ExpressRoute, Azure Site Recovery dados são enviados por essa conexão privada.  Quando um cliente do Azure está se conectando ao Azure usando uma conexão VPN, os dados são replicados entre o local e o cofre dos serviços de recuperação com segurança pela Internet.

Essa transferência de dados de rede segura remove os requisitos de risco de segurança e mitigação para gerenciar soluções tradicionais de armazenamento de backup externo, como mídia de fita.

### <a name="data-retention-periods"></a>Períodos de retenção de dados

Um período de retenção de backup mínimo de três meses é recomendado, no entanto, os períodos de retenção mais longos são normalmente necessários. O backup do Azure pode fornecer até 9999 cópias de um backup. Se um único backup do Azure de uma instância protegida tiver sido feito diariamente, isso permitiria a retenção de 27 anos de backups diários. Os backups mensais individuais de uma instância protegida permitem por 833 anos de retenção. Como os dados de backup são antigos e backups menos granulares são mantidos ao longo do tempo, a janela de retenção total para os dados de backup aumenta.  O Azure não limita o período de tempo que os dados podem permanecer em um cofre dos serviços de recuperação do Azure, apenas o número total de backups por instância. Também não há nenhuma diferença de desempenho entre a restauração de backups novos ou antigos, cada restauração leva o mesmo período de tempo para ocorrer.

O cofre dos serviços de recuperação do Azure tem várias políticas padrão de backup e retenção em vigor.  A equipe administrativa também pode criar políticas personalizadas de backup e retenção.

![Política de backup do Azure](media/create-policy.png)

É necessário encontrar um equilíbrio entre a frequência de backup e os requisitos de retenção de longo prazo ao configurar as políticas de retenção e backup do Azure.

### <a name="backup-and-restore-testing"></a>Teste de backup e restauração

O ISM recomenda o teste de dados de backup para garantir que os dados protegidos sejam válidos quando uma restauração ou um failover for necessário. O backup do Azure e o Azure Site Recovery também fornecem a capacidade de testar dados protegidos após o backup ou a replicação. Os dados gerenciados pelo backup do Azure podem ser restaurados para um local indicado e a consistência dos dados pode ser validada.

Azure Site Recovery tem recursos embutidos para executar testes de failover. As cargas de trabalho replicadas para o cofre dos serviços de recuperação podem ser restauradas para um ambiente do Azure indicado. O ambiente de restauração de destino pode ser totalmente isolado de qualquer ambiente de produção para garantir que não haja nenhum impacto nos sistemas de produção durante a execução de um teste. Depois que o teste for concluído, o ambiente de teste e todos os recursos poderão ser excluídos imediatamente para reduzir os custos operacionais.

O teste de failover e a validação podem ser automatizados usando o serviço de automação do Azure integrado à plataforma Azure. Isso permite que o teste de failover seja agendado para ocorrer automaticamente para garantir que os dados sejam replicados com êxito para o Azure.

## <a name="next-steps"></a>Passos seguintes

Examine o artigo sobre como [garantir a segurança com Azure Policy](azure-policy.md).
