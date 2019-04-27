---
title: Recursos de Azure dados de cliente do Cofre de chaves - Azure Key Vault | Documentos da Microsoft
description: Saiba mais sobre os dados do cliente no Cofre de chaves
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 12492fad419fdc703ddabcf2288be282f2fd47be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60305483"
---
# <a name="azure-key-vault-customer-data-features"></a>Recursos de dados de cliente do Azure Key Vault

O Azure Key Vault recebe dados de clientes durante a criação ou atualização de cofres, chaves, segredos, certificados e contas de armazenamento gerido. Esses dados de cliente são diretamente visíveis no portal do Azure e através da API REST. Dados dos clientes podem ser editados ou eliminados por atualizar ou eliminar o objeto que contém os dados.

Aceder aos registos de sistema são gerados quando um utilizador ou aplicação acede ao Cofre de chaves. Registos de acesso detalhadas estão disponíveis para clientes que utilizam as informações do Azure.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identificar os dados do cliente

As seguintes informações identificam os dados de cliente no Azure Key Vault:

- Políticas de acesso para o Azure Key Vault contenham IDs de objeto que representa os utilizadores, grupos ou aplicações
- Assuntos de certificado podem incluir endereços de e-mail ou outro utilizador ou identificadores organizacionais
- Contactos do certificado podem conter endereços de e-mail do utilizador, nomes ou números de telefone
- Emissores de certificados podem conter endereços de e-mail, nomes, números de telefone, as credenciais da conta e detalhes institucionais
- Etiquetas arbitrárias podem ser aplicadas a objetos no Azure Key Vault. Estes objetos incluem cofres, chaves, segredos, certificados e contas de armazenamento. As etiquetas utilizadas podem conter dados pessoais
- Registos de acesso do Cofre de chaves do Azure contêm os IDs de objeto [UPNs](../active-directory/hybrid/plan-connect-userprincipalname.md)e endereços IP para cada chamada à REST API
- Registos de diagnóstico do Azure Key Vault podem conter IDs de objeto e endereços IP para chamadas da REST API

## <a name="deleting-customer-data"></a>A eliminação de dados do cliente

O mesmo REST APIs, experiência de Portal e SDKs utilizados para criar cofres, chaves, segredos, certificados e contas de armazenamento gerido, também são possível atualizar e eliminar estes objetos.

Eliminação de forma recuperável permite-lhe recuperar dados eliminados durante 90 dias após a eliminação. Ao utilizar a eliminação de forma recuperável, os dados podem ser eliminados permanentemente antes dos 90 dias, período de retenção expira, executando uma operação de remoção. Se o cofre ou a subscrição tiver sido configurada para bloquear remover operações, não é possível eliminar permanentemente os dados até que tenha passado o período de retenção agendado.

## <a name="exporting-customer-data"></a>Exportar dados do cliente

As APIs REST do mesmo, experiência do portal e SDKs que são utilizados para criar cofres, chaves, segredos e certificados e também a geridos de contas de armazenamento permitem-lhe ver e exportar esses objetos.

O Azure Key Vault, o registo de acesso é uma funcionalidade opcional que pode ser ativada para gerar registos para cada chamada à REST API. Estes registos serão transferidos para uma conta de armazenamento na sua subscrição em que aplicar a política de retenção que cumpre os requisitos da sua organização.

O Azure Key Vault os registos de diagnóstico que contêm dados pessoais podem ser obtidos ao fazer um pedido de exportação no portal de privacidade do utilizador. Este pedido deve ser feito pelo administrador de inquilinos.

## <a name="next-steps"></a>Passos Seguintes

- [Registo do Cofre de Chaves do Azure](key-vault-logging.md)

- [Descrição geral da eliminação de forma recuperável de Cofre de chaves do Azure](key-vault-soft-delete-cli.md)

- [Operações de chave de Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Operações secretas do Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Certificados do Key Vault e as políticas do Azure](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Operações de conta de armazenamento do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
