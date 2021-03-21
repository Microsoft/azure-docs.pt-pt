---
title: Recomendações de segurança para armazenamento de filas
titleSuffix: Azure Storage
description: Saiba mais sobre as recomendações de segurança para o armazenamento de filas. Implementar esta orientação irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada.
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: db0e033adf553c25c6b7b401f8d0df1a2cd5995f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592165"
---
# <a name="security-recommendations-for-queue-storage"></a>Recomendações de segurança para armazenamento de filas

Este artigo contém recomendações de segurança para o armazenamento de filas. A implementação destas recomendações irá ajudá-lo a cumprir as suas obrigações de segurança, conforme descrito no nosso modelo de responsabilidade partilhada. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, consulte [responsabilidades partilhadas para a computação em nuvem.](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)

Algumas das recomendações incluídas neste artigo podem ser monitorizadas automaticamente pelo Azure Security Center. O Centro de Segurança Azure é a primeira linha de defesa na proteção dos seus recursos em Azure. Para obter informações sobre o Centro de Segurança Azure, veja [o que é o Centro de Segurança Azure?](../../security-center/security-center-introduction.md)

O Azure Security Center analisa periodicamente o estado de segurança dos seus recursos Azure para identificar potenciais vulnerabilidades de segurança. Em seguida, fornece-lhe recomendações sobre como abordá-las. Para obter mais informações sobre as recomendações do Azure Security Center, consulte [as recomendações de segurança no Centro de Segurança Azure.](../../security-center/security-center-recommendations.md)

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Utilize o modelo de implementação do Gestor de Recursos Azure | Criar novas contas de armazenamento utilizando o modelo de implementação do Azure Resource Manager para melhorias importantes de segurança, incluindo controlo de acesso baseado em funções superiores a Azure (Azure RBAC) e auditoria, implementação e governação baseadas em recursos, acesso a identidades geridas, acesso ao Cofre chave Azure para segredos, e autenticação baseada em Azure e autorização de acesso a dados e recursos de Armazenamento Azure. Se possível, migrar as contas de armazenamento existentes que utilizam o modelo clássico de implementação para utilizar o Azure Resource Manager. Para obter mais informações sobre o Azure Resource Manager, consulte [a visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md). | - |
| Permitir proteção avançada de ameaças para todas as suas contas de armazenamento | A proteção avançada de ameaças para o Azure Storage fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Os alertas de segurança são desencadeados no Centro de Segurança do Azure quando ocorrem anomalias na atividade e são também enviados por e-mail para administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças. Para obter mais informações, consulte [a proteção avançada de ameaças para o armazenamento Azure](../common/azure-defender-storage-configure.md). | [Sim](../../security-center/security-center-remediate-recommendations.md) |
| Limite a assinatura de acesso partilhado (SAS) apenas às ligações HTTPS | Exigir HTTPS quando um cliente usa um token SAS para aceder a dados de fila ajuda a minimizar o risco de escutas. Para obter mais informações, consulte [Grant acesso limitado aos recursos de Armazenamento Azure usando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Utilize o Azure Ative Directory (Azure AD) para autorizar o acesso aos dados da fila | A Azure AD proporciona uma segurança superior e facilidade de utilização sobre a autorização da Chave Partilhada para autorizar pedidos de armazenamento de fila. Para obter mais informações, consulte [Autoriza o acesso a blobs e filas Azure usando o Azure Ative Directory](../common/storage-auth-aad.md). | - |
| Tenha em mente o principal de menor privilégio ao atribuir permissões a um diretor de segurança Azure AD via Azure RBAC | Ao atribuir uma função a um utilizador, grupo ou aplicação, conceda a esse principal de segurança apenas as permissões necessárias para que possam desempenhar as suas tarefas. Limitar o acesso aos recursos ajuda a prevenir o uso indevido e malicioso dos seus dados. | - |
| Proteja as chaves de acesso à sua conta com cofre de chaves Azure | A Microsoft recomenda a utilização do Azure AD para autorizar pedidos para o Azure Storage. No entanto, se tiver de utilizar a autorização da Chave Partilhada, então proteja as chaves da sua conta com o Cofre da Chave Azure. Podes recuperar as chaves do cofre da chave em tempo de execução, em vez de as guardares com a tua aplicação. | - |
| Regenerar as chaves da sua conta periodicamente | A rotação das chaves da conta reduz periodicamente o risco de expor os seus dados a atores mal-intencionados. | - |
| Tenha em mente o principal de menor privilégio ao atribuir permissões a um SAS | Ao criar um SAS, especifique apenas as permissões que são necessárias pelo cliente para desempenhar a sua função. Limitar o acesso aos recursos ajuda a prevenir o uso indevido e malicioso dos seus dados. | - |
| Tenha um plano de revogação em vigor para qualquer SAS que emite aos clientes | Se um SAS estiver comprometido, irá querer revogar o SAS o mais rapidamente possível. Para revogar uma delegação de utilizadores SAS, revogue a chave da delegação do utilizador para invalidar rapidamente todas as assinaturas associadas a essa chave. Para revogar um serviço SAS que esteja associado a uma política de acesso armazenada, pode eliminar a política de acesso armazenada, mudar o nome da apólice ou alterar o seu prazo de validade para um tempo que está no passado. Para obter mais informações, consulte [Grant acesso limitado aos recursos de Armazenamento Azure usando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).  | - |
| Se um serviço SAS não estiver associado a uma política de acesso armazenada, então decida o prazo de validade para uma hora ou menos | Um serviço SAS que não esteja associado a uma política de acesso armazenada não pode ser revogado. Por esta razão, recomenda-se a limitação do tempo de validade para que o SAS seja válido por uma hora ou menos. | - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Configure a versão mínima exigida de Segurança da Camada de Transporte (TLS) para uma conta de armazenamento.  | Exigir que os clientes utilizem uma versão mais segura do TLS para fazer pedidos contra uma conta de Armazenamento Azure, configurando a versão mínima de TLS para essa conta. Para obter mais informações, consulte [a versão mínima exigida de Segurança da Camada de Transporte (TLS) para uma conta de armazenamento](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)| - |
| Ativar a opção **de transferência Secure necessária** em todas as suas contas de armazenamento | Quando ativa a opção **de transferência Secure necessária,** todos os pedidos feitos contra a conta de armazenamento devem ser feitos sobre ligações seguras. Quaisquer pedidos feitos sobre HTTP falharão. Para mais informações, consulte [Exigir transferência segura no Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | [Sim](../../security-center/security-center-remediate-recommendations.md) |
| Ativar regras de firewall | Configure as regras de firewall para limitar o acesso à sua conta de armazenamento a pedidos originários de endereços ou intervalos IP especificados, ou de uma lista de sub-redes numa rede virtual Azure (VNet). Para obter mais informações sobre a configuração das regras de firewall, consulte [as firewalls de armazenamento Configure Azure e redes virtuais](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | - |
| Permitir serviços fidedignos da Microsoft para aceder à conta de armazenamento | A ligação das regras de firewall para a sua conta de armazenamento bloqueia os pedidos de dados por padrão, a menos que os pedidos sejam originários de um serviço que opera dentro de um Azure VNet ou de endereços IP públicos permitidos. Os pedidos que estão bloqueados incluem os de outros serviços Azure, do portal Azure, de serviços de registo e métricas, e assim por diante. Pode permitir pedidos de outros serviços da Azure adicionando uma exceção para permitir que serviços confiáveis da Microsoft acedam à conta de armazenamento. Para obter mais informações sobre a adição de uma exceção para serviços de confiança da Microsoft, consulte [firewalls de armazenamento Configure Azure e redes virtuais](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).| - |
| Utilizar pontos finais privados | Um ponto final privado atribui um endereço IP privado do seu Azure VNet à conta de armazenamento. Protege todo o tráfego entre o seu VNet e a conta de armazenamento sobre o Private Link. Para obter mais informações sobre os pontos finais privados, consulte [Connect private a uma conta de armazenamento utilizando um ponto final privado Azure](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| Utilize tags de serviço VNet | Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam. Para obter mais informações sobre etiquetas de serviço suportadas pelo Azure Storage, consulte [as etiquetas de serviço Azure.](../../virtual-network/service-tags-overview.md) Para um tutorial que mostre como usar tags de serviço para criar regras de rede de saída, consulte [restringir o acesso aos recursos do PaaS](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Limitar o acesso à rede a redes específicas | Limitar o acesso à rede a redes que aloquem clientes que necessitem de acesso reduz a exposição dos seus recursos a ataques de rede. | [Sim](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

| Recomendação | Comentários | Centro de Segurança |
|-|----|--|
| Acompanhe como os pedidos são autorizados | Habilitar o registo de armazenamento Azure para acompanhar como cada pedido feito contra o Azure Storage foi autorizado. Os registos indicam se um pedido foi feito anonimamente, utilizando um token OAuth 2.0, utilizando uma chave partilhada, ou utilizando uma assinatura de acesso partilhado (SAS). Para obter mais informações, consulte [o registo de análises a Azure Storage.](../common/storage-analytics-logging.md) | - |

## <a name="next-steps"></a>Passos seguintes

- [Documentação de segurança do Azure](../../security/index.yml)
- [Documentação de desenvolvimento segura.](../../security/develop/index.yml)
