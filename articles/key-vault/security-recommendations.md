---
title: Recomendações de segurança para cofre de chaves Azure
description: Recomendações de segurança para o Cofre chave Azure. Implementar esta orientação irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: d18dca2d7c44ac9db5ebabc5cc10f2f0b3f2c069
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546362"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Recomendações de segurança para cofre de chaves Azure

Este artigo contém recomendações de segurança para o Cofre chave Azure. A implementação destas recomendações irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, leia [responsabilidades partilhadas para a computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Algumas das recomendações incluídas neste artigo podem ser automaticamente monitorizadas pelo Azure Security Center. O Azure Security Center é a primeira linha de defesa na proteção dos seus recursos em Azure. Analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como abordá-los.

- Para obter mais informações sobre as recomendações do Azure Security Center, consulte recomendações de segurança no Centro de [Segurança Azure.](../security-center/security-center-recommendations.md)
- Para obter informações sobre o Centro de Segurança Azure, consulte o Centro de [Segurança Azure?](../security-center/security-center-intro.md)

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
|Ativar apagar suavemente | [Soft delete](key-vault-ovw-soft-delete.md) permite-lhe recuperar cofres apagados e objetos de cofre |  - |
| Limitar o acesso aos dados do cofre  | Siga o princípio do menor privilégio e limite que os membros da sua organização têm acesso aos dados do cofre |  - |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Limitar o número de utilizadores com acesso ao contribuinte | Se um utilizador tiver permissões do Colaborador para um plano de gestão de cofres chave, o utilizador pode conceder-se acesso ao plano de dados definindo uma política de acesso ao Cofre chave. Deve controlar bem quem tem acesso ao papel de Contribuinte nos seus cofres chave. Certifique-se de que apenas aqueles que necessitam de acesso autorizados podem aceder e gerir os seus cofres. Você pode ler [acesso seguro a um cofre chave](key-vault-secure-your-key-vault.md) | - |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
 Os registos de diagnósticos no Key Vault devem ser ativados | Ative os registos e retenha-os até um ano. Isto permite-lhe recriar trilhas de atividade para fins de investigação quando ocorre um incidente de segurança ou a sua rede está comprometida. | [Sim](../security-center/security-center-identity-access.md) |
| Restrinja quem pode aceder aos seus registos de cofre seledores Azure Key | [Os registos do Cofre de Chaves](key-vault-logging.md) guardam informações sobre as atividades realizadas no seu cofre, tais como criação ou eliminação de cofres, chaves, segredos e podem ser usados durante uma investigação |  - |

## <a name="networking"></a>Redes

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
|Limitar a exposição à rede | O acesso à rede deve limitar-se às redes virtuais utilizadas por soluções que requerem acesso ao cofre. Reveja as informações sobre [os pontos finais do serviço](key-vault-overview-vnet-service-endpoints.md) de rede virtual para o Cofre chave Azure | - |

## <a name="next-steps"></a>Passos seguintes

Consulte o seu fornecedor de candidaturas para ver se existem requisitos de segurança adicionais. Para obter mais informações sobre o desenvolvimento de aplicações seguras, consulte documentação de [desenvolvimento seguro.](../security/fundamentals/abstract-develop-secure-apps.md)
