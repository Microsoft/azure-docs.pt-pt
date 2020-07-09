---
title: Configurar as definições de exportação em API Azure para fHIR
description: Este artigo descreve como configurar as definições de exportação na API Azure para fHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871807"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>Configurar a definição de exportação e exportar os dados para uma conta de armazenamento

A Azure API para FHIR suporta $export comando que lhe permite exportar os dados da Azure API para conta FHIR para uma conta de armazenamento.

Existem quatro etapas envolvidas na realização da exportação na Azure API para o FHIR:

1. Ativar identidade gerida na AZure API para o serviço FHIR
2. Criar uma conta de armazenamento Azure (se não for feito antes) e atribuir permissão à Azure API para fHIR na conta de armazenamento
3. Selecionando a conta de armazenamento em Azure API para fHIR como conta de armazenamento de exportação
4. Execução da exportação invocando $export comando da Azure API para fHIR

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Habilitação da Identidade Gerida na API Azure para FHIR

O primeiro passo para configurar a Azure API para a exportação é permitir a identidade gerida em todo o sistema no serviço. Pode ler tudo sobre identidades geridas em Azure [aqui.](../active-directory/managed-identities-azure-resources/overview.md)

Para tal, navegue pela Azure API para o serviço FHIR e selecione a lâmina de identidade. A alteração do estatuto para On permitirá a identidade gerida na Azure API para o Serviço FHIR.

![Ativar identidade gerida](media/export-data/fhir-mi-enabled.png)

Agora podemos passar ao próximo passo e criar uma conta de armazenamento e atribuir permissão ao nosso serviço.

## <a name="adding-permission-to-storage-account"></a>Adicionar permissão à conta de armazenamento

O próximo passo na exportação é atribuir autorização para a Azure API para o serviço FHIR escrever na conta de armazenamento.

Depois de criarmos uma conta de armazenamento, navegue para a lâmina do Controlo de Acesso (IAM) na conta de armazenamento e selecione Atribuições de Função adicionar

![Ativar identidade gerida](media/export-data/fhir-export-role-assignment.png)

Aqui adicionamos função Storage Blob Data Contributor ao nosso nome de serviço.

![Ativar identidade gerida](media/export-data/fhir-export-role-add.png)

Agora estamos prontos para o próximo passo onde podemos selecionar a conta de armazenamento em Azure API para FHIR como uma conta de armazenamento padrão para $export.

## <a name="selecting-the-storage-account-for-export"></a>Selecionando a conta de armazenamento para $export

O passo final antes de invocar $export comando é atribuir a conta de armazenamento Azure que a Azure API para fHIR utilizará para exportar os dados para. Para isso, navegue para a lâmina de integração em Azure API para o serviço FHIR no portal Azure e selecione a conta de armazenamento 

![Ativar identidade gerida](media/export-data/fhir-export-storage.png)

Depois disso, estamos prontos para exportar os dados usando $export comando.

## <a name="exporting-the-data-using-export-command"></a>Exportação dos dados utilizando o comando $export

Depois de configurarmos a Azure API para a exportação, podemos agora ir e usar o comando $export para exportar os dados para fora do serviço para a conta de armazenamento que especificamos. Para aprender a invocar $export comando no servidor FHIR, leia a documentação sobre $export especificação em[https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> Note que atualmente a API Azure para fHIR apenas suporta a exportação de nível de sistema, tal como definida na especificação de exportação em [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Atualmente também não apoiamos parâmetros de consulta com o $export.

>[!div class="nextstepaction"]
>[Definições adicionais](azure-api-for-fhir-additional-settings.md)