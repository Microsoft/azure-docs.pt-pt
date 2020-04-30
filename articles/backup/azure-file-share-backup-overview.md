---
title: Sobre a cópia de segurança de partilha de ficheiros Azure
description: Saiba como apoiar as ações de ficheiros azure no cofre dos Serviços de Recuperação
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c79100724b882c0682c86070ee74a8726d6b049f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105748"
---
# <a name="about-azure-file-share-backup"></a>Sobre a cópia de segurança de partilha de ficheiros Azure

A cópia de segurança de partilha de ficheiros Azure é uma solução de backup baseada em nuvem nativa que protege os seus dados na nuvem e elimina as despesas adicionais de manutenção envolvidas em soluções de backup no local. O serviço de backup Azure integra-se sem problemas com a sincronização de ficheiros Azure, e permite centralizar os dados de partilha de ficheiros, bem como as suas cópias de segurança. Esta solução simples, fiável e segura permite configurar a proteção para as partilhas de ficheiros da sua empresa em poucos passos simples com a garantia de que pode recuperar os seus dados em caso de qualquer cenário de desastre.

## <a name="key-benefits-of-azure-file-share-backup"></a>Principais benefícios da cópia de segurança de partilha de ficheiros Azure

* Infraestrutura zero: Não é necessária nenhuma implementação para configurar a proteção das suas ações de ficheiros.
* Retenção personalizada: Pode configurar cópias de segurança com retenção diária/semanal/mensal/anual de acordo com os seus requisitos.
* Construído em capacidades de gestão: Pode agendar backups e especificar o período de retenção desejado sem a sobrecarga adicional de poda de dados.
* Restauro instantâneo: A cópia de segurança do ficheiro Azure utiliza imagens de partilha de ficheiros, para que possa selecionar apenas os ficheiros que pretende restaurar instantaneamente.
* Alerta e reportagem: Pode configurar alertas para falhas de backup e restaurar e utilizar a solução de reporte fornecida pela Azure Backup para obter informações sobre backups em todas as partilhas dos seus ficheiros.

## <a name="architecture"></a>Arquitetura

![Arquivo Azure partilha arquitetura de backup](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Como funciona o processo de backup

1. O primeiro passo na configuração de backup para ações do Arquivo Azure é criar um cofre de serviços de recuperação. O cofre dá-lhe uma visão consolidada das cópias de segurança configuradas em diferentes cargas de trabalho.

2. Assim que criar um cofre, o serviço de backup Azure descobre as contas de armazenamento que podem ser registadas com o cofre. Pode selecionar a conta de armazenamento que hospeda as ações de ficheiro que pretende proteger.

3. Depois de selecionar a conta de armazenamento, o serviço De backup Azure lista o conjunto de ações de ficheiropresentes na conta de armazenamento e armazena os seus nomes no catálogo de camadas de gestão.

4. Em seguida, configura a política de backup (agenda e retenção) de acordo com os seus requisitos, e selecione as ações de ficheiro para fazer backup. O serviço de backup Azure regista os horários do plano de controlo para fazer backups regulares.

5. Com base na política especificada, o programador de backup Azure aciona cópias de segurança na hora programada. Como parte desse trabalho, o instantâneo de partilha de ficheiros é criado usando a API de partilha de ficheiros. Apenas o URL instantâneo é armazenado na loja de metadados.

    >[!NOTE]
    >Os dados da partilha de ficheiros não são transferidos para o serviço de backup, uma vez que o serviço de backup cria e gere instantâneos que fazem parte da sua conta de armazenamento, e as cópias de segurança não são transferidas para o cofre.

6. Pode restaurar o conteúdo da partilha de ficheiros Azure (ficheiros individuais ou a parte completa) a partir de instantâneos disponíveis na partilha de ficheiros de origem. Uma vez desencadeada a operação, o URL instantâneo é recuperado da loja de metadados e os dados são listados e transferidos do instantâneo de origem para a parte de ficheiro-alvo da sua escolha.

7. Os dados de backup e restauro de trabalho são empurrados para o serviço de monitorização de backup Azure. Isto permite-lhe monitorizar as cópias de segurança da nuvem para as suas partilhas de ficheiros num único dashboard. Além disso, também pode configurar alertas ou notificações de e-mail quando a saúde de backup é afetada. Os e-mails são enviados através do serviço de e-mail Azure.

## <a name="backup-costs"></a>Custos de backup

Atualmente será cobrado apenas para instantâneos, uma vez que a cópia de segurança de partilha de ficheiros Azure é uma solução baseada em instantâneos. Os custos de armazenamento incorridos para instantâneos são cobrados juntamente com o Uso de Ficheiros Azure de acordo com os detalhes de preços [aqui](https://azure.microsoft.com/pricing/details/storage/files/)mencionados .

## <a name="next-steps"></a>Passos seguintes

* Saiba como [apoiar as ações de ficheiros da Azure](backup-afs.md)
* Encontre respostas a [Perguntas sobre o backup de Ficheiros Azure](backup-azure-files-faq.md)
