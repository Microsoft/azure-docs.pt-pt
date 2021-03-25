---
title: Configurar as definições de exportação em API Azure para fHIR
description: Este artigo descreve como configurar as definições de exportação na API Azure para fHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: matjazl
ms.openlocfilehash: ee110420c697afb6ecad857ba823c61d03c6be6c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046996"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Configurar a definição de exportação e configurar a conta de armazenamento

A Azure API para FHIR suporta $export comando que lhe permite exportar os dados da Azure API para conta FHIR para uma conta de armazenamento.

Existem três etapas envolvidas na configuração da exportação na API Azure para o FHIR:

1. Ativar identidade gerida na Azure API para o serviço FHIR.
2. Criar uma conta de armazenamento Azure (se não for feito antes) e atribuir permissão à Azure API para fHIR na conta de armazenamento.
3. Selecionando a conta de armazenamento na Azure API para fHIR como conta de armazenamento de exportação.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Habilitação da Identidade Gerida na API Azure para FHIR

O primeiro passo para configurar a Azure API para a exportação é permitir a identidade gerida em todo o sistema no serviço. Para obter mais informações sobre identidades geridas em Azure, consulte [Sobre identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Para tal, vá à Azure API para o serviço FHIR e selecione **Identidade**. A alteração do estatuto para **On** permitirá a identidade gerida na Azure API para o Serviço FHIR.

![Ativar identidade gerida](media/export-data/fhir-mi-enabled.png)

Agora, pode passar para o próximo passo criando uma conta de armazenamento e atribuindo permissão ao nosso serviço.

## <a name="adding-permission-to-storage-account"></a>Adicionar permissão à conta de armazenamento

O próximo passo na exportação é atribuir autorização para a Azure API para o serviço FHIR escrever na conta de armazenamento.

Depois de criar uma conta de armazenamento, vá ao **Access Control (IAM)** na conta de armazenamento e selecione **Adicionar a atribuição de função**.

![Atribuição de funções de exportação](media/export-data/fhir-export-role-assignment.png)

É aqui que irá adicionar o contributo de **dados de armazenamento** de armazenamento ao nosso nome de serviço e, em seguida, selecione **Save**.

![Adicionar papel](media/export-data/fhir-export-role-add.png)

Agora está pronto para selecionar a conta de armazenamento em Azure API para FHIR como uma conta de armazenamento padrão para $export.

## <a name="selecting-the-storage-account-for-export"></a>Selecionando a conta de armazenamento para $export

O passo final é atribuir a conta de armazenamento Azure que a Azure API utilizará para exportar os dados para. Para isso, vá à **Integração** na Azure API para o serviço FHIR e selecione a conta de armazenamento.

![Armazenamento de exportação FHIR](media/export-data/fhir-export-storage.png)

Depois de completar este último passo, está agora pronto para exportar os dados usando $export comando.

> [!Note]
> Apenas as contas de armazenamento na mesma subscrição que para a AZure API para fHIR podem ser registadas como destino para $export operações.

Para obter mais informações sobre configurar as definições da base de dados, aceder ao controlo, permitir a verificação de registos de diagnóstico e utilizar cabeçalhos personalizados para adicionar dados aos registos de auditoria, consulte:

>[!div class="nextstepaction"]
>[Definições adicionais](azure-api-for-fhir-additional-settings.md)
