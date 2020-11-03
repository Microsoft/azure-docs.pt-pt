---
title: Recomendações de segurança para o Azure Key Vault
description: Recomendações de segurança para Azure Key Vault. Implementar esta orientação irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: f96df14fb754578b58fb67ac116ac79cff3ffdcb
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289847"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Recomendações de segurança para o Azure Key Vault

Este artigo contém recomendações de segurança para o Cofre da Chave Azure. A implementação destas recomendações irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, leia [responsabilidades partilhadas na computação em nuvem.](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)

Algumas das recomendações incluídas neste artigo podem ser monitorizadas automaticamente pelo Azure Security Center. O Centro de Segurança Azure é a primeira linha de defesa na proteção dos seus recursos em Azure. Analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como abordá-las.

- Para obter mais informações sobre as recomendações do Azure Security Center, consulte [as recomendações de segurança no Centro de Segurança Azure.](../../security-center/security-center-recommendations.md)
- Para obter informações sobre o Azure Security Center consulte o [Centro de Segurança Azure?](../../security-center/security-center-introduction.md)

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
|Ativar a eliminação suave | [A eliminação suave](soft-delete-overview.md) permite-lhe recuperar abóbadas apagadas e objetos de abóbada |  - |
| Limitar o acesso aos dados do cofre  | Siga o princípio do menor privilégio e limite que os membros da sua organização têm acesso aos dados do cofre |  - |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Limitar o número de utilizadores com acesso ao contribuinte | Se um utilizador tiver permissões de Contribuição para um plano de gestão de cofre chave, o utilizador pode conceder-se acesso ao plano de dados definindo uma política de acesso ao Cofre de Chaves. Deve controlar rigorosamente quem tem acesso ao papel do Contribuinte aos seus cofres chave. Certifique-se de que apenas aqueles que precisam de acesso a pessoas autorizadas podem aceder e gerir os seus cofres. Pode ler [acesso seguro a um cofre de chaves)](secure-your-key-vault.md) | - |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
 Os registos de diagnósticos no Key Vault devem ser ativados | Ativar os registos e retê-los até um ano. Isto permite-lhe recriar pistas de atividade para fins de investigação quando ocorre um incidente de segurança ou a sua rede está comprometida. | [Sim](../../security-center/security-center-identity-access.md) |
| Restringir quem pode aceder aos seus registos de cofre da Chave Azure | [Troncos de cofre de chaves](logging.md)) guardar informações sobre as atividades realizadas no seu cofre, tais como criação ou eliminação de cofres, chaves, segredos e pode ser usado durante uma investigação |  - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
|Limitar a exposição à rede | O acesso à rede deve limitar-se às redes virtuais utilizadas por soluções que exijam acesso ao cofre. Reveja as informações sobre [os pontos finais do serviço de rede virtual para o Cofre da Chave Azure](overview-vnet-service-endpoints.md)) | - |

## <a name="next-steps"></a>Passos seguintes

Consulte o seu fornecedor de aplicações para ver se existem requisitos de segurança adicionais. Para obter mais informações sobre o desenvolvimento de aplicações seguras, consulte [a Documentação de Desenvolvimento Seguro](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).