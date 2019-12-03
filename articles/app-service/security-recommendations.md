---
title: Recomendações de segurança
description: Implemente as recomendações de segurança para ajudar a atender suas obrigações de segurança, conforme indicado em nosso modelo de responsabilidade compartilhada. Melhore a segurança do seu aplicativo.
author: barclayn
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e3f901fd78436b42729607d38f0ddb533f31cd30
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684116"
---
# <a name="security-recommendations-for-app-service"></a>Recomendações de segurança para o serviço de aplicativo

Este artigo contém recomendações de segurança para Azure App serviço. Implementar essas recomendações ajudará você a atender suas obrigações de segurança, conforme descrito em nosso modelo de responsabilidade compartilhada, e melhorará a segurança geral para suas soluções de aplicativo Web. Para obter mais informações sobre o que a Microsoft faz para atender às responsabilidades do provedor de serviços, leia [segurança de infraestrutura do Azure](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Geral

| Recomendação | Comentários |
|-|-|----|
| Mantenha-se atualizado | Use as versões mais recentes das plataformas com suporte, linguagens de programação, protocolos e estruturas. |

## <a name="identity-and-access-management"></a>Gestão de identidades e acesso

| Recomendação | Comentários |
|-|----|
| Desabilitar acesso anônimo | A menos que você precise dar suporte a solicitações anônimas, desabilite o acesso anônimo. Para obter mais informações sobre Azure App opções de autenticação de serviço, consulte [autenticação e autorização no serviço Azure app](overview-authentication-authorization.md).|
| Exigir autenticação | Sempre que possível, use o módulo de autenticação do serviço de aplicativo em vez de escrever código para lidar com a autenticação e a autorização. Consulte [autenticação e autorização no serviço de Azure app](overview-authentication-authorization.md). |
| Proteger recursos de back-end com acesso autenticado | Você pode usar a identidade do usuário ou usar uma identidade de aplicativo para se autenticar em um recurso de back-end. Quando você opta por usar uma identidade de aplicativo, use uma [identidade gerenciada](overview-managed-identity.md).
| Exigir autenticação de certificado de cliente | A autenticação de certificado de cliente melhora a segurança permitindo apenas conexões de clientes que podem se autenticar usando certificados fornecidos por você. |

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários |
|-|-|
| Redirecionar HTTP para HTTPs | Por padrão, os clientes podem se conectar a aplicativos Web usando HTTP ou HTTPS. É recomendável redirecionar HTTP para HTTPs porque HTTPS usa o protocolo SSL/TLS para fornecer uma conexão segura, que é criptografada e autenticada. |
| Criptografar a comunicação com os recursos do Azure | Quando seu aplicativo se conecta aos recursos do Azure, como o [banco de dados SQL](https://azure.microsoft.com/services/sql-database/) ou o [armazenamento do Azure](/azure/storage/), a conexão permanece no Azure. Como a conexão passa pela rede compartilhada no Azure, você sempre deve criptografar toda a comunicação. |
| Exigir a versão mais recente do TLS possível | Desde 2018 novos aplicativos de serviço Azure App usam o TLS 1,2. As versões mais recentes do TLS incluem aprimoramentos de segurança em relação às versões de protocolo mais antigas. |
| Usar FTPS | O serviço de aplicativo dá suporte a FTP e FTPS para implantar seus arquivos. Use FTPS em vez de FTP quando possível. Quando um ou ambos os protocolos não estão em uso, você deve [desabilitá-los](deploy-ftp.md#enforce-ftps). |
| Proteger os dados das aplicações | Não armazene segredos do aplicativo, como credenciais de banco de dados, tokens de API ou chaves privadas em seu código ou arquivos de configuração. A abordagem comumente aceita é acessá-las como [variáveis de ambiente](https://wikipedia.org/wiki/Environment_variable) usando o padrão padrão no idioma de sua escolha. No Azure App Service, você pode definir variáveis de ambiente por meio de [configurações de aplicativo](web-sites-configure.md) e [cadeias de conexão](web-sites-configure.md). As configurações do aplicativo e as cadeias de conexão são armazenadas criptografadas no Azure. As configurações do aplicativo são descriptografadas somente antes de serem injetadas na memória do processo do aplicativo quando o aplicativo é iniciado. As chaves de criptografia são giradas regularmente. Como alternativa, você pode integrar seu aplicativo de serviço Azure App com [Azure Key Vault](/azure/key-vault/) para o gerenciamento avançado de segredos. Ao [acessar o Key Vault com uma identidade gerenciada](../key-vault/tutorial-web-application-keyvault.md), seu aplicativo do serviço de aplicativo pode acessar com segurança os segredos de que você precisa. |

## <a name="networking"></a>Funcionamento em Rede

| Recomendação | Comentários |
|-|-|
| Usar restrições de IP estático | Azure App serviço no Windows permite que você defina uma lista de endereços IP que têm permissão para acessar seu aplicativo. A lista de permissões pode incluir endereços IP individuais ou um intervalo de endereços IP definidos por uma máscara de sub-rede. Para obter mais informações, consulte [Azure app restrições de IP estático do serviço](app-service-ip-restrictions.md).  |
| Usar o tipo de preço isolado | Exceto para o tipo de preço isolado, todas as camadas executam seus aplicativos na infraestrutura de rede compartilhada no serviço Azure App. A camada isolada dá a você total isolamento de rede executando seus aplicativos dentro de um [ambiente de serviço de aplicativo](environment/intro.md)dedicado. Um ambiente do serviço de aplicativo é executado em sua própria instância da [rede virtual do Azure](/azure/virtual-network/).|
| Usar conexões seguras ao acessar recursos locais | Você pode usar [conexões híbridas](app-service-hybrid-connections.md), [integração de rede virtual](web-sites-integrate-with-vnet.md)ou [ambiente de serviço de aplicativo](environment/intro.md) para se conectar a recursos locais. |
| Limitar a exposição ao tráfego de rede de entrada | Os grupos de segurança de rede permitem restringir o acesso à rede e controlar o número de pontos de extremidade expostos. Para obter mais informações, consulte [como controlar o tráfego de entrada para um ambiente do serviço de aplicativo](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários |
|-|-|
|Usar a camada Standard da central de segurança do Azure | A [central de segurança do Azure](../security-center/security-center-app-services.md) é integrada nativamente ao serviço de Azure app. Ele pode executar avaliações e fornecer recomendações de segurança. |

## <a name="next-steps"></a>Passos seguintes

Verifique com seu provedor de aplicativos para ver se há requisitos de segurança adicionais. Para obter mais informações sobre como desenvolver aplicativos seguros, consulte a [documentação de desenvolvimento seguro](../security/fundamentals/abstract-develop-secure-apps.md).
