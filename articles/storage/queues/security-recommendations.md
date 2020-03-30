---
title: Recomendações de segurança para armazenamento de fila
titleSuffix: Azure Storage
description: Conheça as recomendações de segurança para o armazenamento da fila. Implementar esta orientação irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada.
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060902"
---
# <a name="security-recommendations-for-queue-storage"></a>Recomendações de segurança para armazenamento de fila

Este artigo contém recomendações de segurança para armazenamento de fila. A implementação destas recomendações irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, leia [responsabilidades partilhadas para a computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Algumas das recomendações incluídas neste artigo podem ser automaticamente monitorizadas pelo Azure Security Center. O Azure Security Center é a primeira linha de defesa na proteção dos seus recursos em Azure. Para obter informações sobre o Centro de Segurança Azure, consulte o Centro de [Segurança Azure?](../../security-center/security-center-intro.md)

O Azure Security Center analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como abordá-los. Para obter mais informações sobre as recomendações do Azure Security Center, consulte recomendações de segurança no Centro de [Segurança Azure.](../../security-center/security-center-recommendations.md)

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Utilize o modelo de implementação do Gestor de Recursos Azure | Criar novas contas de armazenamento utilizando o modelo de implementação do Gestor de Recursos Azure para importantes melhorias de segurança, incluindo controlo de acesso superior (RBAC) e auditoria, implantação e governação baseadas em Gestor de Recursos, acesso a identidades geridas, acesso para azure Key Vault para segredos, e autenticação e autorização baseadas em Azure AD para acesso a dados e recursos de Armazenamento Azure. Se possível, migrar as contas de armazenamento existentes que usam o modelo de implementação clássico para utilizar o Gestor de Recursos Azure. Para mais informações sobre o Gestor de Recursos Azure, consulte a [visão geral do Gestor de Recursos do Azure.](/azure/azure-resource-manager/resource-group-overview) | - |
| Ativar a opção **de transferência Segura necessária** em todas as suas contas de armazenamento | Quando ativa a opção necessária à **transferência Segura,** todos os pedidos feitos contra a conta de armazenamento devem ser realizados sobre ligações seguras. Quaisquer pedidos feitos sobre http falharão. Para mais informações, consulte [Exigir transferência segura no Armazenamento Azure](../common/storage-require-secure-transfer.md). | [Sim](../../security-center/security-center-sql-service-recommendations.md) |
| Permitir proteção avançada de ameaças para todas as suas contas de armazenamento | A proteção avançada contra ameaças para o Armazenamento Azure fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de acesso ou exploração de contas de armazenamento. Os alertas de segurança são desencadeados no Centro de Segurança do Azure quando ocorrem anomalias na atividade e são também enviados por e-mail para administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças. Para mais informações, consulte [A proteção avançada contra ameaças para o Armazenamento Azure](../common/storage-advanced-threat-protection.md). | [Sim](../../security-center/security-center-sql-service-recommendations.md) |
| Limite fichas de assinatura de acesso partilhado (SAS) apenas a ligações HTTPS | Exigir HTTPS quando um cliente usa um token SAS para aceder a dados de fila ajuda a minimizar o risco de escutas. Para mais informações, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Utilizar o Diretório Ativo Azure (Azure AD) para autorizar o acesso aos dados da fila | A Azure AD proporciona uma segurança e facilidade de utilização superiores sobre a Chave Partilhada para autorizar pedidos de armazenamento em fila. Para mais informações, consulte [Autorizar o acesso a blobs e filas Azure utilizando o Diretório Ativo Azure](../common/storage-auth-aad.md). | - |
| Tenha em mente o principal de menor privilégio ao atribuir permissões a um diretor de segurança da AD Azure via RBAC | Ao atribuir uma função a um utilizador, grupo ou aplicação, conceda a esse responsável de segurança apenas as permissões necessárias para que possam desempenhar as suas tarefas. Limitar o acesso aos recursos ajuda a prevenir tanto o uso involuntário como o uso malicioso dos seus dados. | - |
| Proteja as chaves de acesso à sua conta com o Cofre de Chaves Azure | A Microsoft recomenda a utilização de Anúncios Azure para autorizar pedidos de armazenamento Azure. No entanto, se tiver de utilizar a autorização da Chave Partilhada, proteja as chaves da sua conta com o Cofre de Chaves Azure. Pode saquear as chaves do cofre chave em tempo de funcionamento, em vez de as guardar com a sua aplicação. | - |
| Regenerar periodicamente as chaves da sua conta | Rodar as teclas da conta periodicamente reduz o risco de expor os seus dados a atores maliciosos. | - |
| Tenha em mente o principal de menor privilégio ao atribuir permissões a um SAS | Ao criar um SAS, especifique apenas as permissões que são necessárias pelo cliente para desempenhar a sua função. Limitar o acesso aos recursos ajuda a prevenir tanto o uso involuntário como o uso malicioso dos seus dados. | - |
| Tenha um plano de revogação em vigor para qualquer SAS que emita aos clientes | Se um SAS estiver comprometido, vai querer revogar o SAS o mais rapidamente possível. Para revogar uma delegação de utilizadores SAS, revogue a chave da delegação de utilizadores para invalidar rapidamente todas as assinaturas associadas a essa chave. Para revogar um serviço SAS associado a uma política de acesso armazenada, pode eliminar a política de acesso armazenada, mudar o nome da apólice ou alterar o seu tempo de validade para um tempo que está no passado. Para mais informações, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).  | - |
| Se um serviço SAS não estiver associado a uma política de acesso armazenada, então determina o tempo de validade para uma hora ou menos | Um serviço SAS que não esteja associado a uma política de acesso armazenada não pode ser revogado. Por esta razão, recomenda-se a limitação do tempo de validade para que o SAS seja válido por uma hora ou menos. | - |

## <a name="networking"></a>Redes

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Ativar regras de firewall | Configure as regras de firewall para limitar o acesso à sua conta de armazenamento a pedidos originários de endereços ou gamas IP especificados, ou a partir de uma lista de subredes numa Rede Virtual Azure (VNet). Para obter mais informações sobre a configuração das regras de firewall, consulte as definições de [proxy e firewall do Ficheiro Azure](../files/storage-sync-files-firewall-and-proxy.md). | - |
| Permitir que serviços confiáveis da Microsoft acedam à conta de armazenamento | A aplicação das regras de firewall para a sua conta de armazenamento bloqueia os pedidos de dados por defeito, a menos que os pedidos tenham origem num serviço que opera dentro de uma Rede Virtual Azure (VNet) ou de endereços IP públicos permitidos. Os pedidos que estão bloqueados incluem os de outros serviços Azure, do portal Azure, dos serviços de exploração madeireira e métrica, e assim por diante. Pode autorizar pedidos de outros serviços do Azure adicionando uma exceção para permitir que serviços confiáveis da Microsoft acedam à conta de armazenamento. Para obter mais informações sobre a adição de uma exceção para serviços fidedignos da Microsoft, consulte as definições de [proxy e firewall do Web Do Ficheiro Azure.](../files/storage-sync-files-firewall-and-proxy.md)| - |
| Use pontos finais privados | Um ponto final privado atribui um endereço IP privado da sua Rede Virtual Azure (VNet) à conta de armazenamento. Protege todo o tráfego entre o seu VNet e a conta de armazenamento sobre um link privado. Para mais informações sobre pontos finais privados, consulte o Connect em privado para uma conta de [armazenamento utilizando o Azure Private Endpoint](../../private-link/create-private-endpoint-storage-portal.md). | - |
| Limitar o acesso da rede a redes específicas | Limitar o acesso à rede a redes que acolhem clientes que exigem acesso reduz a exposição dos seus recursos a ataques de rede. | [Sim](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Exploração madeireira/monitorização

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Acompanhe como os pedidos são autorizados | Ative a exploração de armazenamento azure para acompanhar a forma como cada pedido feito contra o Armazenamento Azure foi autorizado. Os registos indicam se um pedido foi feito de forma anónima, utilizando um token OAuth 2.0, utilizando a Chave Partilhada, ou utilizando uma assinatura de acesso partilhado (SAS). Para mais informações, consulte [a exploração de analíticos do Azure.](../common/storage-analytics-logging.md) | - |

## <a name="next-steps"></a>Passos seguintes

- [Documentação de segurança azure](https://docs.microsoft.com//azure/security/)
- [Documentação de desenvolvimento segura.](https://docs.microsoft.com/azure/security/develop/)
