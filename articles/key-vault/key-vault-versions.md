---
title: Versões do Key Vault
description: As várias versões do Azure Key Vault
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 3672d3f0b62978460720509a72e19d1948a39dc5
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57402449"
---
# <a name="key-vault-versions"></a>Versões do Key Vault

## <a name="2016-10-01---managed-storage-account-keys"></a>2016-10-01 - chaves de conta de armazenamento gerido

Verão 2017 - funcionalidade de chaves de conta de armazenamento foi adicionada a integração mais fácil com o armazenamento do Azure. Consulte o tópico de descrição geral para obter mais informações, [descrição geral de chaves de conta de armazenamento geridas pelo](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>2016-10-01 - Soft-delete

Verão 2017 - funcionalidade de eliminação de forma recuperável adicionados para proteção de dados melhorada dos seus cofres de chaves e o Cofre de chaves objetos. Consulte o tópico de descrição geral para obter mais informações, [descrição geral da eliminação de forma recuperável](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>2015-06-01 - gestão de certificados

Gestão de certificados é adicionada como um recurso para a versão de DG 2015-06-01 de 26 de Setembro de 2016.

## <a name="2015-06-01---general-availability"></a>2015-06-01 - disponibilidade geral

Disponibilidade geral versão 2015-06-01, anunciado em 24 de Junho de 2015.

As seguintes alterações foram feitas nesta versão:

- Eliminar uma chave - "utilizar" campo removido.
- Obtenha informações sobre uma chave - "utilizar" campo removido.
- Importar uma chave para um cofre - "utilizar" campo removido.
- Restaurar uma chave - "utilizar" campo removido.
- Alterado "RSA_OAEP" para "RSA-OAEP" para algoritmos RSA. Ver [sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-pré-visualização 

Segundo preview versão 2015-02-01-pré-visualização, anunciámos a 20 de Abril de 2015. Para obter mais informações, consulte [atualizações de API do REST](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) postagem de blog.

Foram atualizadas as seguintes tarefas:

- Liste as chaves num cofre - paginação foi adicionado suporte para a operação.
- Lista as versões de uma chave - adicionar operação para listar as versões de uma chave.
- Lista os segredos num cofre - suporte de paginação adicionados.
- As versões de um segredo de lista - Adicionar operação para listar as versões de um segredo.
- Todas as operações - adicionado criado/atualizado carimbos a atributos.
- Criar um segredo - adicionado Content-Type para segredos.
- Criar uma chave - adicionadas etiquetas como informações opcionais.
- Criar um segredo - adicionadas etiquetas como informações opcionais.
- Atualizar uma chave - adicionadas etiquetas como informações opcionais.
- Atualizar um segredo - adicionadas etiquetas como informações opcionais.
- Mudou o tamanho máximo para segredos de 10 mil a 25 mil Bytes. Ver, [sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-pré-visualização

Primeira pré-visualização versão 2014-12-08-pré-visualização, anunciado no dia 8 de Janeiro de 2015.

## <a name="see-also"></a>Consulte também
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
