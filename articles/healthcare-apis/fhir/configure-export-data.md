---
title: Configurar as definições de exportação em API Azure para fHIR
description: Este artigo descreve como configurar as definições de exportação na API Azure para fHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: bb728b81d4dad5d880c27d1ebe8f85f1508f8231
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019758"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Configurar a definição de exportação e configurar a conta de armazenamento

A Azure API para FHIR suporta $export comando que lhe permite exportar os dados da Azure API para conta FHIR para uma conta de armazenamento.

Existem três etapas envolvidas na configuração da exportação na API Azure para o FHIR:

1. Ativar identidade gerida na AZure API para o serviço FHIR
2. Criar uma conta de armazenamento Azure (se não for feito antes) e atribuir permissão à Azure API para fHIR na conta de armazenamento
3. Selecionando a conta de armazenamento em Azure API para fHIR como conta de armazenamento de exportação

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Habilitação da Identidade Gerida na API Azure para FHIR

O primeiro passo para configurar a Azure API para a exportação é permitir a identidade gerida em todo o sistema no serviço. Pode ler tudo sobre identidades geridas em Azure [aqui.](../../active-directory/managed-identities-azure-resources/overview.md)

Para tal, navegue pela Azure API para o serviço FHIR e selecione a lâmina de identidade. A alteração do estatuto para On permitirá a identidade gerida na Azure API para o Serviço FHIR.

![Ativar identidade gerida](media/export-data/fhir-mi-enabled.png)

Agora podemos passar ao próximo passo e criar uma conta de armazenamento e atribuir permissão ao nosso serviço.

## <a name="adding-permission-to-storage-account"></a>Adicionar permissão à conta de armazenamento

O próximo passo na exportação é atribuir autorização para a Azure API para o serviço FHIR escrever na conta de armazenamento.

Depois de criarmos uma conta de armazenamento, navegue para a lâmina do Controlo de Acesso (IAM) na conta de armazenamento e selecione Atribuições de Função adicionar

![Atribuição de funções de exportação](media/export-data/fhir-export-role-assignment.png)

Aqui adicionamos função Storage Blob Data Contributor ao nosso nome de serviço.

![Adicionar papel](media/export-data/fhir-export-role-add.png)

Agora estamos prontos para o próximo passo onde podemos selecionar a conta de armazenamento em Azure API para FHIR como uma conta de armazenamento padrão para $export.

## <a name="selecting-the-storage-account-for-export"></a>Selecionando a conta de armazenamento para $export

O passo final é atribuir a conta de armazenamento Azure que a AZure API utilizará para exportar os dados para. Para isso, navegue para a lâmina de integração em Azure API para o serviço FHIR no portal Azure e selecione a conta de armazenamento

![Armazenamento de exportação FHIR](media/export-data/fhir-export-storage.png)

Depois disso, estamos prontos para exportar os dados usando $export comando.

>[!div class="nextstepaction"]
>[Definições adicionais](azure-api-for-fhir-additional-settings.md)
