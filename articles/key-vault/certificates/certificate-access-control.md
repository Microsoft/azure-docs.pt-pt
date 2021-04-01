---
title: Sobre o controlo de certificados de cofre de chaves Azure
description: Visão geral do controlo de certificados Azure Key Vault
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92128664"
---
# <a name="certificate-access-control"></a>Controlo de Acesso a Certificados

 O controlo de acesso para certificados é gerido pelo Key Vault e é disponibilizado pelo Key Vault que contém esses certificados. A política de controlo de acesso para certificados distingue-se das políticas de controlo de acesso para chaves e segredos no mesmo Cofre-Chave. Os utilizadores podem criar um ou mais cofres para deter certificados, para manter o cenário apropriado segmentação e gestão de certificados.  

 As seguintes permissões podem ser utilizadas, numa base por princípio, na entrada de controlo de acesso aos segredos num cofre de chaves, e espelham de perto as operações permitidas num objeto secreto:  

- Permissões para operações de gestão de certificados
  - **obter**: Obtenha a versão atual do certificado, ou qualquer versão de um certificado
  - **lista**: Listar os certificados atuais ou versões de um certificado  
  - **atualização**: Atualizar um certificado
  - **criar**: Criar um certificado de Cofre de Chaves
  - **importação**: Material de certificado de importação num certificado Key Vault
  - **eliminar**: Eliminar um certificado, a sua política e todas as suas versões  
  - **recuperar**: Recuperar um certificado eliminado
  - **backup**: Cópia de segurança de um certificado em um cofre chave
  - **restaurar**: Restaurar um certificado de apoio para um cofre chave
  - **contactos de gestão**: Gerir os contactos do certificado key Vault  
  - **manageissuers**: Gerir as autoridades/emitentes de certificados de cofre-chave
  - **getissuers**: Obter um certificado autoridades/emitentes
  - **listissuers**: Listar as autoridades/emitentes de um certificado  
  - **setissuers**: Criar ou atualizar as autoridades/emitentes de um certificado de cofre-chave  
  - **deleteissuers**: Eliminar as autoridades/emitentes de um certificado de cofre de chaves  
 
- Permissões para operações privilegiadas
  - **purga:** Purga (eliminar permanentemente) um certificado eliminado

Para obter mais informações, consulte as operações do [Certificado na referência API do Cofre-Chave](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="troubleshoot"></a>Resolução de problemas
Pode ver erro devido à falta da política de acesso. Por ```Error type : Access denied or user is unauthorized to create certificate``` exemplo, Para resolver este erro, terá de adicionar certificados/criar permissão.

## <a name="next-steps"></a>Passos seguintes

- [Sobre o Key Vault](../general/overview.md)
- [Acerca de chaves, segredos e certificados](../general/about-keys-secrets-certificates.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
