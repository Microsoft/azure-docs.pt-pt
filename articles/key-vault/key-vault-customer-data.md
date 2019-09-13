---
title: Azure Key Vault recursos de dados do cliente – Azure Key Vault | Microsoft Docs
description: Saiba mais sobre os dados do cliente em Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 67e1aeab4211249075b51bd0138d7875756a3483
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883317"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault recursos de dados do cliente

Azure Key Vault recebe dados do cliente durante a criação ou atualização de cofres, chaves, segredos, certificados e contas de armazenamento gerenciadas. Esses dados do cliente são diretamente visíveis na portal do Azure e por meio da API REST. Os dados do cliente podem ser editados ou excluídos atualizando ou excluindo o objeto que contém os dados.

Os logs de acesso do sistema são gerados quando um usuário ou aplicativo acessa Key Vault. Os logs de acesso detalhado estão disponíveis para clientes que usam o Azure insights.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificando dados do cliente

As informações a seguir identificam os dados do cliente dentro do Azure Key Vault:

- Políticas de acesso para Azure Key Vault conter IDs de objeto que representam usuários, grupos ou aplicativos
- As entidades do certificado podem incluir endereços de email ou outros identificadores organizacionais ou de usuário
- Os contatos de certificado podem conter endereços de email do usuário, nomes ou números de telefone
- Os emissores de certificado podem conter endereços de email, nomes, números de telefone, credenciais de conta e detalhes organizacionais
- Marcas arbitrárias podem ser aplicadas a objetos no Azure Key Vault. Esses objetos incluem cofres, chaves, segredos, certificados e contas de armazenamento. As marcas usadas podem conter dados pessoais
- Azure Key Vault logs de acesso contêm IDs de objeto, [UPNs](../active-directory/hybrid/plan-connect-userprincipalname.md)e endereços IP para cada chamada à API REST
- Azure Key Vault logs de diagnóstico podem conter IDs de objeto e endereços IP para chamadas à API REST

## <a name="deleting-customer-data"></a>A eliminação de dados do cliente

As mesmas APIs REST, experiência do portal e SDKs usados para criar cofres, chaves, segredos, certificados e contas de armazenamento gerenciadas, também são capazes de atualizar e excluir esses objetos.

A exclusão reversível permite recuperar dados excluídos por 90 dias após a exclusão. Ao usar a exclusão reversível, os dados podem ser excluídos permanentemente antes do período de retenção de 90 dias expiram executando uma operação de limpeza. Se o cofre ou a assinatura tiver sido configurado para bloquear operações de limpeza, não será possível excluir dados permanentemente até que o período de retenção agendado tenha passado.

## <a name="exporting-customer-data"></a>Exportar dados do cliente

As mesmas APIs REST, a experiência do portal e os SDKs usados para criar cofres, chaves, segredos, certificados e contas de armazenamento gerenciadas também permitem que você exiba e exporte esses objetos.

O log de acesso do Azure Key Vault é um recurso opcional que pode ser ativado para gerar logs para cada chamada à API REST. Esses logs serão transferidos para uma conta de armazenamento em sua assinatura na qual você aplicará a política de retenção que atende aos requisitos da sua organização.

Azure Key Vault logs de diagnóstico que contêm dados pessoais podem ser recuperados fazendo uma solicitação de exportação no portal de privacidade do usuário. Essa solicitação deve ser feita pelo administrador de locatários.

## <a name="next-steps"></a>Passos Seguintes

- [Registo do Cofre de Chaves do Azure](key-vault-logging.md)

- [Visão geral da exclusão reversível Azure Key Vault](key-vault-soft-delete-cli.md)

- [Operações de Azure Key Vault chave](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Azure Key Vault operações secretas](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault certificados e políticas](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault operações de conta de armazenamento](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
