---
title: Volte a subir uma quinta SharePoint para Azure com DPM
description: Este artigo fornece uma visão geral da proteção do servidor dPM/Azure backup de uma fazenda SharePoint para O Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 0199495e3b0eb002e58c096ed9abf05d46f43f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054114"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Volte a subir uma quinta SharePoint para Azure com DPM

Você faz o backing de uma fazenda SharePoint para o Microsoft Azure usando System Center Data Protection Manager (DPM) da mesma forma que você faz o backback de outras fontes de dados. O Azure Backup oferece flexibilidade na programação de backup para criar pontos de backup diários, semanais, mensais ou anuais e oferece opções de política de retenção para vários pontos de backup. A DPM fornece a capacidade de armazenar cópias de discos locais para objetivos de tempo de recuperação rápida (RTO) e de armazenar cópias para o Azure para retenção económica e a longo prazo.

Apoiar o SharePoint para Azure com dPM é um processo muito semelhante para apoiar o SharePoint para dPM localmente. No presente artigo serão observadas considerações particulares sobre o Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>O SharePoint suportaversões suportadas e cenários de proteção conexos

Para obter uma lista das versões suportadas do SharePoint e das versões do DPM necessárias para criar uma cópia de segurança das mesmas, consulte [Que conteúdos pode o DPM armazenar em cópia de segurança?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup)

## <a name="before-you-start"></a>Antes de começar

Há algumas coisas que precisa confirmar antes de voltar a uma fazenda SharePoint para Azure.

### <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, certifique-se de que cumpriu todos os [pré-requisitos para a utilização](backup-azure-dpm-introduction.md#prerequisites-and-limitations) do Microsoft Azure Backup para proteger as cargas de trabalho. Algumas tarefas para pré-requisitos incluem: criar um cofre de reserva, baixar credenciais de cofre, instalar o Agente de Backup Azure e registar o Servidor de Backup DPM/Azure com o cofre.

Pré-requisitos e limitações adicionais podem ser encontrados no Back up SharePoint com artigo [DPM.](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations)

## <a name="configure-backup"></a>Configurar a cópia de segurança

Para criar cópias de segurança do farm do SharePoint, configure a proteção para o SharePoint através do ConfigureSharePoint.exe e, em seguida, crie um grupo de proteção no DPM. Para obter instruções, consulte [configurar o Backup](https://docs.microsoft.com//system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) na documentação do DPM.

## <a name="monitoring"></a>Monitorização

Para monitorizar o trabalho de backup, siga as instruções de [monitorização de dpm backup](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring)

## <a name="restore-sharepoint-data"></a>Restaurar dados do SharePoint

Para aprender como restaurar um item SharePoint a partir de um disco com DPM, consulte [restaurar os dados do SharePoint](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Restaurar uma base de dados SharePoint a partir de Azure usando DPM

1. Para recuperar uma base de dados de conteúdo sharePoint, navegue por vários pontos de recuperação (como mostrado anteriormente), e selecione o ponto de recuperação que pretende restaurar.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações de catálogo do SharePoint disponíveis.

   > [!NOTE]
   > Uma vez que a exploração SharePoint está protegida para retenção a longo prazo no Azure, não há informações de catálogo (metadados) disponíveis no servidor DPM. Como resultado, sempre que uma base de dados de conteúdo sharePoint ponto-a-tempo precisa de ser recuperada, precisa de catalogar novamente a quinta SharePoint.
   >
   >
3. Clique em **Re-catalogar**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela de estado do **Cloud Recatalog** abre-se.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Após a catalogação estar terminada, o estado muda para *O Sucesso*. Clique em **Fechar**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Clique no objeto SharePoint mostrado no separador De **recuperação** dPM para obter a estrutura da base de dados de conteúdos. Clique no item para a direita e, em seguida, clique em **Recuperar**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Neste ponto, siga os passos de recuperação no início deste artigo para recuperar uma base de dados de conteúdo sharePoint a partir do disco.

## <a name="switching-the-front-end-web-server"></a>Mudar o servidor web front-end

Se tiver mais de um servidor web frontal e pretender mudar o servidor que o DPM utiliza para proteger a exploração, siga as instruções para [mudar o Servidor Web front-end](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server).

## <a name="next-steps"></a>Passos seguintes

* [Servidor de backup Azure e DPM - FAQ](backup-azure-dpm-azure-server-faq.md)
* [Troubleshoot System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md) (Resolver Problemas do System Center Data Protection Manager)
