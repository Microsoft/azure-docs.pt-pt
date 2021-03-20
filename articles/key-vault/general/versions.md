---
title: Versões Azure Key Vault
description: As várias versões do Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 2396aac67a17352987d9d6e3b49535c19ffe8917
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792383"
---
# <a name="key-vault-versions"></a>Versões do Key Vault

Eis as novidades com o Cofre da Chave Azure. Novas funcionalidades e melhorias também são anunciadas no [canal Azure updates Key Vault](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="june-2020"></a>Junho de 2020

O Monitor Azure para o Cofre de Chaves está agora em pré-visualização.  O Azure Monitor fornece uma monitorização abrangente dos seus cofres chave, proporcionando uma visão unificada dos seus pedidos de Cofre chave, desempenho, falhas e latência. Para obter mais informações, consulte [O Monitor Azure para o Cofre de Chaves (pré-visualização).](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>Maio de 2020

O Key Vault "bring your own key" (BYOK) está agora geralmente disponível. Consulte a [especificação Azure Key Vault BYOK](../keys/byok-specification.md)e aprenda a [importar chaves protegidas pelo HSM para o Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Março de 2020

Pontos finais privados agora disponíveis na pré-visualização. O Azure Private Link Service permite-lhe aceder ao Azure Key Vault e aos serviços de cliente/parceiro hospedados no Azure durante um Private Endpoint na sua rede virtual.  Saiba como integrar o [Cofre de Chaves com a Ligação Privada Azure](private-link-service.md).

## <a name="2019"></a>2019

- Lançamento da próxima geração Azure Key Vault SDKs. Por exemplo, veja os quickstarts secretos do Azure Key Vault para [Python](../secrets/quick-create-python.md), [.NET,](../secrets/quick-create-net.md) [Java](../secrets/quick-create-java.md)e [Node.js](../secrets/quick-create-node.md)
- Novas políticas da Azure para gerir certificados de cofre chave. Consulte as [definições de Azure Policy incorporadas para o Cofre de Chaves](../policy-reference.md).
- Extensão da máquina virtual Azure Key Vault agora geralmente disponível.  Consulte [a extensão da máquina virtual Key Vault para](../../virtual-machines/extensions/key-vault-linux.md) a [extensão da máquina virtual](../../virtual-machines/extensions/key-vault-windows.md)Linux e Key Vault para windows .
- Gestão de segredos orientados para eventos para Azure Key Vault agora disponível em Azure Event Grid. Para obter mais informações, consulte [o esquema da Grelha de Eventos para eventos no Cofre da Chave Azure](.. /.. /event-grid/event-schema-key-vault.md], e aprender a [receber e responder às notificações-chave do cofre com a Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Novas funcionalidades e integrações lançadas este ano:

- Integração com Funções Azure. Para um cenário de aproveitamento das [funções do Azure](../../azure-functions/index.yml) para operações de cofre chave, consulte [Automatizar a rotação de um segredo](../secrets/tutorial-rotation.md). 
- [Integração com a Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Com isto, a Azure Databricks suporta agora dois tipos de âmbitos secretos: Azure Key Vault apoiado e apoiado por Databricks. Para obter mais informações, consulte [Criar um âmbito secreto apoiado por Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Pontos finais de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Novas funcionalidades lançadas este ano:

- Chaves de conta de armazenamento geridas. Recurso De Chaves de Conta de Armazenamento acrescentou uma integração mais fácil com o Azure Storage. Consulte o tópico geral para obter mais informações, [visão geral das chaves de conta de armazenamento geridos](../secrets/overview-storage-keys.md).
- Apagar suavemente. A função de eliminação suave melhora a proteção de dados dos cofres e dos objetos chave do cofre. Consulte o tópico geral para obter mais informações, eliminar a visão geral do [soft-delete](./soft-delete-overview.md).

## <a name="2015"></a>2015

Novas funcionalidades lançadas este ano:
- Gestão de certificados. Adicionado como recurso à versão GA 2015-06-01 no dia 26 de setembro de 2016.

A Disponibilidade Geral (versão 2015-06-01) foi anunciada em 24 de junho de 2015. Foram efetuadas as seguintes alterações nesta versão: 
- Eliminar uma chave - campo de "utilização" removido.
- Obtenha informações sobre uma chave - campo de "uso" removido.
- Importe uma chave para um cofre - campo de "uso" removido.
- Restaurar uma chave - campo de "utilização" removido.     
- Alterou "RSA_OAEP" para "RSA-OAEP" para algoritmos RSA. Veja [mais sobre chaves, segredos e certificados.](about-keys-secrets-certificates.md)    
 
A segunda versão de pré-visualização (versão 2015-02-01)foi anunciada a 20 de abril de 2015. Para obter mais informações, consulte o post de blog [REST API Update.](/archive/blogs/kv/rest-api-update) Foram atualizadas as seguintes tarefas:
 
- Lista as chaves num cofre- apoio de paginação adicionado à operação.
- Liste as versões de uma chave - operação adicionada para listar as versões de uma chave.  
- List secrets in a vault - adicionado suporte de paginação.
- List versions of a secret - adicione operação para listar as versões de um segredo.  
- Todas as operações - Adicionados os tempos criados/atualizados aos atributos.  
- Crie um segredo - adicione conteúdo-tipo aos segredos.
- Crie uma chave - etiquetas adicionadas como informação opcional.
- Crie um segredo - etiquetas adicionadas como informação opcional.
- Atualizar uma chave - etiquetas adicionadas como informação opcional.
- Atualize um segredo - etiquetas adicionadas como informação opcional.
- Mudou o tamanho máximo para segredos de 10 K a 25 K Bytes. Ver, [sobre chaves, segredos e certificados.](about-keys-secrets-certificates.md)    
 
## <a name="2014"></a>2014
 
A primeira versão de pré-visualização (versão 2014-12-08)foi anunciada a 8 de janeiro de 2015.  
 
## <a name="next-steps"></a>Passos seguintes

- [Acerca de chaves, segredos e certificados](about-keys-secrets-certificates.md)
- [Chaves](../keys/index.yml)
- [Segredos](../secrets/index.yml)
- [Certificados](../certificates/index.yml)