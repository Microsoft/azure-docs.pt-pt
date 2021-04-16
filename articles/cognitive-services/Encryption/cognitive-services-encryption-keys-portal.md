---
title: Chaves Customer-Managed para serviços cognitivos
titleSuffix: Cognitive Services
description: Aprenda a usar o portal Azure para configurar chaves geridas pelo cliente com o Azure Key Vault. As teclas geridas pelo cliente permitem criar, rodar, desativar e revogar os controlos de acesso.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2021
ms.author: egeaney
ms.openlocfilehash: 7c7476a3ab885e9c127cbd571ad723864bf0d898
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534569"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Configure as chaves geridas pelo cliente com cofre chave Azure para serviços cognitivos

O processo para permitir Customer-Managed Chaves com Cofre chave Azure para Serviços Cognitivos varia de acordo com o produto. Utilize estes links para instruções específicas de serviço:

## <a name="vision"></a>Visão

* [Encriptação personalizada da Visão de dados em repouso](../custom-vision-service/encrypt-data-at-rest.md)
* [Encriptação dos Serviços faciais de dados em repouso](../face/encrypt-data-at-rest.md)
* [Encriptação do Reconhecimento de Formulários de Dados em repouso](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>Linguagem

* [Encriptação do serviço de compreensão de linguagem de dados em repouso](../LUIS/encrypt-data-at-rest.md)
* [Encriptação de dados do QnA Maker em repouso](../QnAMaker/encrypt-data-at-rest.md)
* [Encriptação do tradutor de dados em repouso](../translator/encrypt-data-at-rest.md)

## <a name="speech"></a>Voz

* [Encriptação da fala dos dados em repouso](../speech-service/speech-encryption-of-data-at-rest.md)

## <a name="decision"></a>Decisão

* [Encriptação do Moderador de Conteúdo dos dados em repouso](../Content-Moderator/encrypt-data-at-rest.md)
* [Encriptação personalizada de dados em repouso](../personalizer/encrypt-data-at-rest.md)
* [Métricas Encriptação de dados em repouso](../metrics-advisor/encryption.md)

## <a name="next-steps"></a>Passos seguintes

* [O que é Azure Key Vault?](../../key-vault/general/overview.md)
* [Formulário de pedido chave de Customer-Managed serviços cognitivos](https://aka.ms/cogsvc-cmk)