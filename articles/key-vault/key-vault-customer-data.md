---
title: Funcionalidades de dados de clientes do Azure Key Vault - Cofre chave Azure / Microsoft Docs
description: Conheça os dados do cliente no Cofre chave
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 67e1aeab4211249075b51bd0138d7875756a3483
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883317"
---
# <a name="azure-key-vault-customer-data-features"></a>Funcionalidades de dados de clientes do Azure Key Vault

O Azure Key Vault recebe dados do cliente durante a criação ou atualização de cofres, chaves, segredos, certificados e contas de armazenamento geridas. Estes dados do Cliente são diretamente visíveis no portal Azure e através da API REST. Os dados do cliente podem ser editados ou eliminados atualizando ou eliminando o objeto que contém os dados.

Os registos de acesso ao sistema são gerados quando um utilizador ou aplicação acede ao Key Vault. Os registos de acesso detalhados estão disponíveis para os clientes que utilizam o Azure Insights.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificação dos dados dos clientes

As seguintes informações identificam os dados dos clientes dentro do Cofre chave Azure:

- As políticas de acesso ao Cofre chave Azure contêm iDs de objetos que representam utilizadores, grupos ou aplicações
- Os sujeitos do certificado podem incluir endereços de e-mail ou outros identificadores de utilizador ou organizacional
- Os contactos do certificado podem conter endereços de e-mail, nomes ou números de telefone do utilizador
- Os emitentes de certificados podem conter endereços de e-mail, nomes, números de telefone, credenciais de conta e detalhes organizacionais
- As etiquetas arbitrárias podem ser aplicadas a Objetos no Cofre chave Azure. Estes objetos incluem cofres, chaves, segredos, certificados e contas de armazenamento. As etiquetas utilizadas podem conter dados pessoais
- Os registos de acesso do Cofre chave Azure contêm iDs de objetos, [UPNs](../active-directory/hybrid/plan-connect-userprincipalname.md)e endereços IP para cada chamada rest API
- Os registos de diagnóstico do Cofre de Chaves Azure podem conter IDs de objetos e endereços IP para chamadas REST API

## <a name="deleting-customer-data"></a>Apagar dados dos clientes

As mesmas APIs REST, experiência portal e SDKs usados para criar cofres, chaves, segredos, certificados e contas de armazenamento geridas, também são capazes de atualizar e apagar estes objetos.

A eliminação suave permite-lhe recuperar os dados apagados durante 90 dias após a eliminação. Ao utilizar o soft delete, os dados podem ser eliminados permanentemente antes do período de retenção de 90 dias expirar através da execução de uma operação de purga. Se o cofre ou a subscrição tiver sido configurado para bloquear as operações de purga, não é possível eliminar permanentemente os dados até que o período de retenção programado tenha passado.

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

As mesmas APIs REST, experiência de portal e SDKs que são usados para criar cofres, chaves, segredos, certificados e contas de armazenamento geridas também permitem ver e exportar estes objetos.

O registo de acesso ao Cofre de Chaves Azure é uma funcionalidade opcional que pode ser ativada para gerar registos para cada chamada rest API. Estes registos serão transferidos para uma conta de armazenamento na sua subscrição onde aplica a política de retenção que satisfaz os requisitos da sua organização.

Os registos de diagnóstico do Cofre chave Azure que contenham dados pessoais podem ser recuperados fazendo um pedido de exportação no portal de Privacidade do Utilizador. Este pedido deve ser feito pelo administrador inquilino.

## <a name="next-steps"></a>Passos seguintes

- [Registo do Cofre de Chaves do Azure](key-vault-logging.md)

- [Descrição geral da eliminação recuperável do Azure Key Vault](key-vault-soft-delete-cli.md)

- [Operações-chave do cofre de chaves Azure](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Operações secretas do Cofre chave Azure](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Certificados e políticas do Cofre chave Azure](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Operações de armazenamento de cofre de chaves Azure](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
