---
title: Apoiar uma quinta sharePoint para Azure com DPM
description: Este artigo fornece uma visão geral da proteção do servidor de backup DPM/Azure de uma fazenda SharePoint para Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 7661d64e487c8b8badca240852d17bcf736ba8cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254436"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Apoiar uma quinta sharePoint para Azure com DPM

Faz uma pressão sobre uma quinta SharePoint para o Microsoft Azure, utilizando o Gestor de Proteção de Dados do System Center (DPM) da mesma forma que faz o back up de outras fontes de dados. O Azure Backup proporciona flexibilidade no calendário de backup para criar pontos de backup diários, semanais, mensais ou mensais e dá-lhe opções de política de retenção para vários pontos de backup. A DPM fornece a capacidade de armazenar cópias de disco locais para objetivos rápidos de tempo de recuperação (RTO) e de armazenar cópias para a Azure para retenção económica e a longo prazo.

O backup do SharePoint para o Azure com o DPM é um processo muito semelhante para apoiar o SharePoint para o DPM localmente. Neste artigo serão anotadas considerações específicas para o Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versões suportadas no SharePoint e cenários de proteção conexos

Para obter uma lista das versões suportadas do SharePoint e das versões do DPM necessárias para criar uma cópia de segurança das mesmas, consulte [Que conteúdos pode o DPM armazenar em cópia de segurança?](/system-center/dpm/dpm-protection-matrix#applications-backup)

## <a name="before-you-start"></a>Antes de começar

Há algumas coisas que precisa de confirmar antes de voltar a uma quinta sharePoint para a Azure.

### <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, certifique-se de que cumpriu todos os [pré-requisitos para usar o Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) para proteger as cargas de trabalho. Algumas tarefas para pré-requisitos incluem: criar um cofre de reserva, baixar credenciais de cofre, instalar o Agente de Backup Azure e registar o DPM/Azure Backup Server com o cofre.

Pré-requisitos e limitações adicionais podem ser encontrados no Back up SharePoint com o artigo [DPM.](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations)

## <a name="configure-backup"></a>Configurar a cópia de segurança

Para criar cópias de segurança do farm do SharePoint, configure a proteção para o SharePoint através do ConfigureSharePoint.exe e, em seguida, crie um grupo de proteção no DPM. Para obter instruções, consulte [a Cópia de Segurança configurada](/system-center/dpm/back-up-sharepoint#configure-backup) na documentação DPM.

## <a name="monitoring"></a>Monitorização

Para monitorizar o trabalho de backup, siga as instruções na [monitorização da cópia de segurança do DPM](/system-center/dpm/back-up-sharepoint#monitoring)

## <a name="restore-sharepoint-data"></a>Restaurar dados do SharePoint

Para aprender a restaurar um item SharePoint a partir de um disco com DPM, consulte [os dados do SharePoint de restauro](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Restaurar uma base de dados SharePoint a partir do Azure utilizando o DPM

1. Para recuperar uma base de dados de conteúdos SharePoint, navegue por vários pontos de recuperação (como mostrado anteriormente) e selecione o ponto de recuperação que pretende restaurar.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Clique duas vezes no ponto de recuperação do SharePoint para mostrar as informações disponíveis do catálogo SharePoint.

   > [!NOTE]
   > Como a quinta SharePoint está protegida para retenção a longo prazo no Azure, nenhuma informação de catálogo (metadados) está disponível no servidor DPM. Como resultado, sempre que uma base de dados de conteúdos do SharePoint pontual precisa de ser recuperada, é necessário catalogar novamente a quinta SharePoint.
   >
   >
3. Selecione **Re-catálogo**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    A janela de estado **do Recatalog de Nuvem** abre-se.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Depois de concluída a catalogação, o estado muda para *Sucesso*. Selecione **Fechar**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Selecione o objeto SharePoint mostrado no separador **recuperação** de DPM para obter a estrutura da base de dados de conteúdos. Clique com o botão direito no item e, em seguida, **selecione Recuperar**.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Neste ponto, siga os passos de recuperação mais cedo neste artigo para recuperar uma base de dados de conteúdos SharePoint do disco.

## <a name="switching-the-front-end-web-server"></a>Mudar o servidor web Front-End

Se tiver mais do que um servidor web frontal e quiser mudar o servidor que o DPM utiliza para proteger a quinta, siga as instruções para [mudar o Front-End Web Server](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server).

## <a name="next-steps"></a>Passos seguintes

* [Azure Backup Server e DPM - FAQ](backup-azure-dpm-azure-server-faq.md)
* [Troubleshoot System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md) (Resolver Problemas do System Center Data Protection Manager)
