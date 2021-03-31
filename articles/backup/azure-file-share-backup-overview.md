---
title: Sobre a Azure file share backup
description: Saiba como apoiar as ações de ficheiros da Azure no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: e955df7662bd18ed1d1d4ec1f0aa6c9474c5386f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89378088"
---
# <a name="about-azure-file-share-backup"></a>Sobre a Azure file share backup

A azure file share backup é uma solução de backup baseada em nuvem nativa que protege os seus dados na nuvem e elimina despesas adicionais de manutenção envolvidas em soluções de backup no local. O serviço Azure Backup integra-se sem problemas com o Azure File Sync e permite centralizar os dados de partilha de ficheiros, bem como as suas cópias de segurança. Esta solução simples, fiável e segura permite-lhe configurar a proteção para as ações dos ficheiros da empresa em alguns passos simples com a garantia de que pode recuperar os seus dados em qualquer cenário de desastre.

## <a name="key-benefits-of-azure-file-share-backup"></a>Principais benefícios da cópia de segurança da partilha de ficheiros Azure

* **Infraestrutura zero**: Não é necessária qualquer implantação para configurar a proteção das suas ações de ficheiros.
* **Retenção personalizada**: Pode configurar backups com retenção diária/semanal/mensal/anual de acordo com os seus requisitos.
* **Incorporado nas capacidades de gestão**: Pode agendar backups e especificar o período de retenção pretendido sem a sobrecarga adicional da poda de dados.
* **Restaurar instantaneamente**: A cópia de segurança da partilha de ficheiros Azure utiliza instantâneos de partilha de ficheiros, para que possa selecionar apenas os ficheiros que pretende restaurar instantaneamente.
* **Alertar e reportar**: Pode configurar alertas para cópias de segurança e restaurar falhas e utilizar a solução de reporte fornecida pela Azure Backup para obter informações sobre cópias de segurança através das partilhas dos seus ficheiros.
* **Proteção contra a eliminação acidental das ações de ficheiros**: O Azure Backup permite a [função de eliminação suave](../storage/files/storage-files-prevent-file-share-deletion.md) num nível de conta de armazenamento com um período de retenção de 14 dias. Mesmo que um ator malicioso elimine a partilha de ficheiros, o conteúdo e pontos de recuperação da partilha de ficheiros (instantâneos) são retidos por um período de retenção configurável, permitindo a recuperação bem sucedida e completa de conteúdos de origem e instantâneos sem perda de dados.

## <a name="architecture"></a>Arquitetura

![Azure file share backup arquitetura](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup

1. O primeiro passo para configurar o backup para as ações de ficheiros Azure é criar um cofre dos Serviços de Recuperação. O cofre dá-lhe uma visão consolidada das cópias de segurança configuradas em diferentes cargas de trabalho.

2. Assim que criar um cofre, o serviço Azure Backup descobre as contas de armazenamento que podem ser registadas no cofre. Pode selecionar a conta de armazenamento que hospeda as ações de ficheiro que pretende proteger.

3. Depois de selecionar a conta de armazenamento, o serviço Azure Backup lista o conjunto de ações de ficheiros presentes na conta de armazenamento e armazena os seus nomes no catálogo de camadas de gestão.

4. Em seguida, configura a política de backup (agendar e retenção) de acordo com os seus requisitos, e selecione as ações de ficheiros para fazer backup. O serviço Azure Backup regista os horários no avião de controlo para fazer cópias de segurança programadas.

5. Com base na política especificada, o agendador de backup Azure ativa cópias de segurança na hora programada. Como parte desse trabalho, o snapshot de partilha de ficheiros é criado usando a API de partilha de ficheiros. Apenas o URL instantâneo é armazenado na loja de metadados.

    >[!NOTE]
    >Os dados de partilha de ficheiros não são transferidos para o serviço de Backup, uma vez que o serviço de Backup cria e gere instantâneos que fazem parte da sua conta de armazenamento, e as cópias de segurança não são transferidas para o cofre.

6. Pode restaurar o conteúdo da partilha de ficheiros Azure (ficheiros individuais ou a parte completa) a partir de instantâneos disponíveis na partilha de ficheiros de origem. Uma vez que a operação é desencadeada, o URL instantâneo é recuperado da loja de metadados e os dados são listados e transferidos do instantâneo de origem para a parte do ficheiro alvo da sua escolha.

7. Se estiver a utilizar o Azure File Sync, o serviço de Cópia de Segurança indica para o serviço Azure File Sync os caminhos dos ficheiros que estão a ser restaurados, o que, em seguida, desencadeia um processo de deteção de mudança de fundo nestes ficheiros. Quaisquer ficheiros que tenham sido alterados são sincronizados até ao ponto final do servidor. Este processo ocorre em paralelo com o restauro original da partilha de ficheiros Azure.

8. Os dados de monitorização do trabalho de backup e restauro são empurrados para o serviço de monitorização de backup Azure. Isto permite-lhe monitorizar as cópias de segurança em nuvem para as suas partilhas de ficheiros num único dashboard. Além disso, também pode configurar alertas ou notificações de e-mail quando a saúde de backup é afetada. Os e-mails são enviados através do serviço de e-mail Azure.

## <a name="backup-costs"></a>Custos de backup

Existem dois custos associados à solução de backup de partilha de ficheiros Azure:

1. **Custo de armazenamento instantâneo**: Os encargos de armazenamento incorridos para instantâneos são faturados juntamente com a utilização dos Ficheiros Azure de acordo com os detalhes de preços [mencionados aqui](https://azure.microsoft.com/pricing/details/storage/files/)

2. **Taxa de Instância Protegida**: A partir de 1 de setembro de 2020, os clientes serão cobrados uma taxa de instância protegida de acordo com os detalhes de preços [aqui mencionados.](https://azure.microsoft.com/pricing/details/backup/) A taxa de instância protegida depende do tamanho total das ações de ficheiros protegidas numa conta de armazenamento.

Para obter estimativas detalhadas para o backup de ações de ficheiros Azure, você pode baixar o estimador de preços de [Azure Backup](https://aka.ms/AzureBackupCostEstimates)detalhado .  

## <a name="next-steps"></a>Passos seguintes

* Saiba como [apoiar as ações de ficheiros da Azure](backup-afs.md)
* Encontre respostas a [Perguntas sobre o backup dos Ficheiros Azure](backup-azure-files-faq.md)
