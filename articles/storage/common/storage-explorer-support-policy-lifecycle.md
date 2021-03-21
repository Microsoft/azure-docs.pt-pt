---
title: Azure Storage Explorer suporta ciclo de vida | Microsoft Docs
description: Visão geral da política de suporte e do ciclo de vida do Azure Storage Explorer
services: storage
author: MRayermannMSFT
manager: jinglouMSFT
ms.service: storage
ms.topic: article
ms.date: 07/10/2020
ms.author: marayerm
ms.openlocfilehash: dc89631208c6a72c4b48d49260f584149d6c4260
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97679945"
---
# <a name="azure-storage-explorer-support-lifecycle-and-policy"></a>Azure Storage Explorer suporta ciclo de vida e política

Este documento abrange o ciclo de vida de suporte e a política do Azure Storage Explorer.

## <a name="update-schedule-and-process"></a>Agenda e processo de atualização

O Azure Storage Explorer é lançado quatro a seis vezes por ano. A Microsoft também pode contornar este calendário para lançar correções para problemas críticos.

O Storage Explorer verifica as atualizações a cada hora e descarrega-as quando estão disponíveis. No entanto, é necessária a aceitação do utilizador para iniciar o processo de instalação. Se os utilizadores optarem por atualizar num momento diferente, podem verificar manualmente a atualização. No Windows e Linux, os utilizadores podem verificar se há atualizações selecionando **Check for Updates** a partir do menu **Ajuda.** No macOS, **verifique se as atualizações** podem ser encontradas no menu da **aplicação.** Os utilizadores também podem ir diretamente à página de descarregamento [do Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para descarregar e instalar a versão mais recente.

Recomendamos vivamente que utilize sempre as versões mais recentes do Storage Explorer. Se um problema o impedir de atualizar para a versão mais recente do Storage Explorer, abra uma edição sobre o nosso [GitHub](https://github.com/microsoft/AzureStorageExplorer).

## <a name="support-lifecycle"></a>Apoiar ciclo de vida

O Storage Explorer é regido pela Política Moderna do [Ciclo de Vida.](https://support.microsoft.com/help/30881/modern-lifecycle-policy) Espera-se que os utilizadores mantenham a instalação do Storage Explorer atualizada. Manter-se atualizado garante que os utilizadores têm as mais recentes capacidades, melhorias de desempenho, segurança e fiabilidade do serviço.

A partir da versão 1.14.1, qualquer versão do Storage Explorer com mais de 12 meses será considerada sem suporte. Todos os lançamentos antes do 1.14.1 serão considerados fora do suporte a partir de 14 de julho de 2021. As versões que estão fora de suporte já não estão garantidas para funcionar plenamente como projetado e esperado. Para uma lista de todos os lançamentos, a data de lançamento e a data de fim de suporte, consulte [Os Lançamentos](#releases).

A partir da versão 1.13.0, será apresentado um alerta na aplicação assim que uma versão estiver a cerca de um mês de ficar sem suporte. O alerta encoraja os utilizadores a atualizarem para a versão mais recente do Storage Explorer. Uma vez que uma versão esteja fora de suporte, o alerta in-app será exibido em cada start-up.

## <a name="releases"></a>Versões

Esta tabela descreve a data de lançamento e a data de suporte para cada lançamento do Azure Storage Explorer.

| Versão do Explorador de Armazenamento  | Data da versão       | Data de fim do suporte |
|:-------------------------:|:------------------:|:-------------------:|
| v1.17.0                   | 15 de dezembro de 2020  | 15 de dezembro de 2021   |
| v1.16.0                   | 10 de novembro de 2020  | 10 de novembro de 2021   |
| v1.15.1                   | 2 de setembro de 2020  | 2 de setembro de 2021   |
| v1.15.0                   | 27 de agosto de 2020    | 27 de agosto de 2021     |
| v1.14.2                   | 16 de julho de 2020      | 16 de julho de 2021       |
| v1.14.1                   | 14 de julho de 2020      | 14 de julho de 2021       |
| v1.14.0                   | 24 de junho de 2020      | 14 de julho de 2021       |
| v1.13.1                   | 18 de maio de 2020       | 14 de julho de 2021       |
| v1.13.0                   | 1 de maio de 2020        | 14 de julho de 2021       |
| v1.12.0                   | 16 de janeiro de 2020   | 14 de julho de 2021       |
| v1.11.2                   | 17 de dezembro de 2019  | 14 de julho de 2021       |
| v1.11.1                   | 20 de novembro de 2019  | 14 de julho de 2021       |
| v1.11.0                   | 4 de novembro de 2019   | 14 de julho de 2021       |
| v1.10.1                   | 19 de setembro de 2019 | 14 de julho de 2021       |
| v1.10.0                   | 12 de setembro de 2019 | 14 de julho de 2021       |
| v1.9.0                    | 1 de julho de 2019       | 14 de julho de 2021       |
| v1.8.1                    | 10 de maio de 2019       | 14 de julho de 2021       |
| v1.8.0                    | 2 de maio de 2019        | 14 de julho de 2021       |
| v1.7.0                    | 5 de março de 2019      | 14 de julho de 2021       |
| v1.6.2                    | 8 de janeiro de 2019    | 14 de julho de 2021       |
| v1.6.1                    | 18 de dezembro de 2018  | 14 de julho de 2021       |
| v1.6.0                    | 4 de dezembro de 2018   | 14 de julho de 2021       |
| v1.5.0                    | 29 de outubro de 2018   | 14 de julho de 2021       |
| v1.4.4                    | 15 de outubro de 2018   | 14 de julho de 2021       |
| v1.4.2                    | Setembro de 24, 2018 | 14 de julho de 2021       |
| v1.4.1                    | 28 de Agosto de 2018    | 14 de julho de 2021       |
| v1.3.1                    | 11 de julho de 2018      | 14 de julho de 2021       |
| v1.2.0                    | 12 de junho de 2018      | 14 de julho de 2021       |
| v1.1.0                    | 9 de maio de 2018        | 14 de julho de 2021       |
| v1.0.0 (e mais velhos)        | 16 de abril de 2018     | 14 de julho de 2021       |
