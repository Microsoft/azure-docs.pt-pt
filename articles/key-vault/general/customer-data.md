---
title: Funcionalidades de dados do cliente do Azure Key Vault - Azure Key Vault Microsoft Docs
description: Conheça os dados do cliente, que o Azure Key Vault recebe durante a criação ou atualização de cofres, chaves, segredos, certificados e contas de armazenamento geridas.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7155e1517ba5ff203f11ee834af3c0d1dd1fa065
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289480"
---
# <a name="azure-key-vault-customer-data-features"></a>Funcionalidades de dados do cliente do Azure Key Vault

O Azure Key Vault recebe dados do cliente durante a criação ou atualização de cofres, piscinas geridas de HSM, chaves, segredos, certificados e contas de armazenamento geridas. Estes dados do Cliente são diretamente visíveis no portal Azure e através da API REST. Os dados do cliente podem ser editados ou eliminados atualizando ou eliminando o objeto que contém os dados.

Os registos de acesso ao sistema são gerados quando um utilizador ou aplicação acede ao Key Vault. Registos de acesso detalhados estão disponíveis para os clientes que usam a Azure Insights.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificar dados do cliente

As seguintes informações identificam os dados dos clientes dentro do Cofre da Chave Azure:

- As políticas de acesso ao Cofre da Chave Azure contêm iDs de objetos que representam utilizadores, grupos ou aplicações
- Os sujeitos de certificado podem incluir endereços de e-mail ou outros identificadores de utilizadores ou organizacionais
- Os contactos do certificado podem conter endereços de e-mail, nomes ou números de telefone do utilizador
- Os emitentes de certificados podem conter endereços de e-mail, nomes, números de telefone, credenciais de conta e detalhes organizacionais
- As etiquetas arbitrárias podem ser aplicadas em Objetos no Cofre da Chave Azure. Estes objetos incluem abóbadas, chaves, segredos, certificados e contas de armazenamento. As tags utilizadas podem conter dados pessoais
- Os registos de acesso do Cofre de Chaves Azure contêm iDs de objetos, [UPNs](../../active-directory/hybrid/plan-connect-userprincipalname.md)e endereços IP para cada chamada de API REST
- Os registos de diagnóstico do Azure Key Vault podem conter iDs de objetos e endereços IP para chamadas DE API

## <a name="deleting-customer-data"></a>Eliminação dos dados dos clientes

As mesmas APIs rest, experiência portal e SDKs usados para criar cofres, chaves, segredos, certificados e contas de armazenamento geridos, também são capazes de atualizar e eliminar estes objetos.

A eliminação suave permite-lhe recuperar dados eliminados durante 90 dias após a eliminação. Ao utilizar a eliminação suave, os dados podem ser permanentemente eliminados antes do período de retenção de 90 dias expirar através da realização de uma operação de purga. Se o cofre ou a subscrição daram configuração para bloquear operações de purga, não é possível apagar permanentemente os dados até que o período de retenção programado tenha passado.

## <a name="exporting-customer-data"></a>Exportação de dados dos clientes

As mesmas APIs DE REST, experiência do portal e SDKs que são usados para criar cofres, chaves, segredos, certificados e contas de armazenamento geridos também permitem visualizar e exportar estes objetos.

O registo de acesso ao Cofre de Chaves Azure é uma funcionalidade opcional que pode ser ligada para gerar registos para cada chamada de API REST. Estes registos serão transferidos para uma conta de armazenamento na sua subscrição onde aplica a política de retenção que satisfaz os requisitos da sua organização.

Os registos de diagnóstico Azure Key Vault que contenham dados pessoais podem ser recuperados fazendo um pedido de exportação no portal privacidade do utilizador. Este pedido deve ser feito pelo administrador do arrendatário.

## <a name="next-steps"></a>Passos seguintes

- [Azure Key Vault Logging](logging.md)

- [Descrição geral da eliminação recuperável do Azure Key Vault](soft-delete-cli.md)

- [Operações chave Azure Key Vault](/rest/api/keyvault/key-operations)

- [Operações secretas do Azure Key Vault](/rest/api/keyvault/secret-operations)

- [Certificados e políticas do Cofre chave Azure](/rest/api/keyvault/certificates-and-policies)

- [Operações de conta de armazenamento Azure Key Vault](/rest/api/keyvault/storage-account-key-operations)