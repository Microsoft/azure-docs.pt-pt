---
title: Recomendações de segurança para o serviço de aplicações do Azure
description: Recomendações de segurança para o serviço de aplicações do Azure. Implementar estas recomendações serão ajuda a atender suas obrigações de segurança, tal como descrito no nosso modelo de responsabilidade partilhada e irá melhorar a segurança geral para as suas soluções de aplicação web.
services: app-service
author: barclayn
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.openlocfilehash: f50532a21d11d2f7142fa3ee7ed08f759df36d52
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207544"
---
# <a name="security-recommendations-for-app-service"></a>Recomendações de segurança para o serviço de aplicações

Este artigo contém recomendações de segurança para o serviço de aplicações do Azure. Implementar estas recomendações serão ajuda a atender suas obrigações de segurança, tal como descrito no nosso modelo de responsabilidade partilhada e irá melhorar a segurança geral para as suas soluções de aplicação Web. Para obter mais informações sobre a Microsoft não para atender às responsabilidades do fornecedor de serviço, leia [segurança de infraestrutura do Azure](../security/azure-security-infrastructure.md).

## <a name="general"></a>Geral

| Recomendação | Comentários |
|-|-|----|
| Mantenha-se atualizado | Utilize as versões mais recentes das plataformas suportadas, linguagens de programação, protocolos e estruturas. |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Recomendação | Comentários |
|-|----|
| Desativar o acesso anónimo | A menos que precisa para suportar pedidos anónimos, desabilitar o acesso anônimo. Para obter mais informações sobre as opções de autenticação do serviço de aplicações do Azure, consulte [autenticação e autorização no serviço de aplicações do Azure](overview-authentication-authorization.md).|
| Exigir a autenticação | Sempre que possível, utilize o módulo de autenticação do serviço de aplicações em vez de escrever código para lidar com autenticação e autorização. Ver [autenticação e autorização no serviço de aplicações do Azure](overview-authentication-authorization.md). |
| Proteger recursos de back-end com o acesso autenticado | Pode utilizar a identidade do utilizador ou utilizar uma identidade de aplicação para autenticar a um recurso de back-end. Quando opta por utilizar uma identidade aplicativo uma [identidade gerida](overview-managed-identity.md).
| Exigir a autenticação de certificado de cliente | Autenticação de certificado de cliente melhora a segurança ao permitir apenas ligações de clientes que podem autenticar a utilização de certificados que fornecer. |

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários |
|-|-|
| Redirecionar HTTP para HTTPs | Por predefinição, os clientes podem ligar a aplicações web, utilizando HTTP ou HTTPS. Recomendamos o redireccionamento de HTTP para HTTPs, porque o HTTPS utiliza o protocolo SSL/TLS para fornecer uma ligação segura, que é tanto criptografadas quanto autenticadas. |
| Encriptar a comunicação para recursos do Azure | Quando a aplicação se liga a recursos do Azure, tal como [base de dados SQL](https://azure.microsoft.com/services/sql-database/) ou [armazenamento do Azure](/azure/storage/), a ligação permanece no Azure. Uma vez que a ligação atravessa a rede partilhada no Azure, deve sempre criptografar todas as comunicações. |
| Exigir a versão mais recente do TLS possível | Uma vez que 2018 novas aplicações de serviço de aplicações do Azure utilizam o TLS 1.2. As versões mais recentes do TLS incluem aprimoramentos de segurança sobre as versões mais antigas do protocolo. |
| Utilizar FTPS | Serviço de aplicações suporta FTP e FTPS para implementar os seus ficheiros. Utilize FTPS em vez de FTP, sempre que possível. Quando um ou ambos os protocolos não estão em utilização, deverá [desativá-las](deploy-ftp.md#enforce-ftps). |
| Proteger os dados das aplicações | Não armazene segredos da aplicação, como credenciais de base de dados, tokens de API ou as chaves privadas em seus arquivos de código ou configuração. A abordagem mais aceita é acessá-los como [variáveis de ambiente](https://wikipedia.org/wiki/Environment_variable) usando o padrão na linguagem de sua escolha. No serviço de aplicações do Azure, pode definir variáveis de ambiente através de [as definições da aplicação](web-sites-configure.md) e [cadeias de ligação](web-sites-configure.md). Definições de aplicações e as cadeias de ligação são armazenadas de forma encriptada no Azure. As definições da aplicação são desencriptadas antes de a ser injetado na memória de processo da sua aplicação quando a aplicação for iniciada. As chaves de encriptação são revezadas regularmente. Em alternativa, pode integrar a sua aplicação de serviço de aplicações do Azure com o [do Azure Key Vault](/azure/key-vault/) para a gestão de segredos avançadas. Por [aceder ao Key Vault com uma identidade gerida](../key-vault/tutorial-web-application-keyvault.md), seu serviço de aplicações pode aceder em segurança os segredos que precisa. |

## <a name="networking"></a>Redes

| Recomendação | Comentários |
|-|-|
| Utilizar as restrições de IP estáticas | Serviço de aplicações do Azure no Windows permite-lhe definir uma lista de endereços IP que têm permissão para aceder à sua aplicação. Lista de permissões pode incluir os endereços IP individuais ou um intervalo de endereços IP definidos por uma máscara de sub-rede. Para obter mais informações, consulte [restrições de IP do Azure App Service estático](app-service-ip-restrictions.md).  |
| Utilizar o escalão de preço isolado | Exceto para o escalão de preço isolado, todos os escalões de executam as suas aplicações na infraestrutura de rede partilhado no serviço de aplicações do Azure. O escalão isolado dá-lhe isolamento de rede completa ao executar as suas aplicações dentro de um dedicado [ambiente do serviço de aplicações](environment/intro.md). Um ambiente de serviço de aplicações é executada na sua própria instância do [rede Virtual do Azure](/azure/virtual-network/).|
| Utilizar ligações seguras ao aceder a recursos no local | Pode usar [ligações híbridas](app-service-hybrid-connections.md), [integração da rede Virtual](web-sites-integrate-with-vnet.md), ou [ambiente de serviço de aplicações](environment/intro.md) para ligar a recursos no local. |
| Limitar a exposição ao tráfego de rede de entrada | Grupos de segurança de rede permitem-lhe restringir o acesso de rede e controlar o número de pontos finais expostos. Para obter mais informações, consulte [como para controle de tráfego de entrada para um ambiente de serviço de aplicações](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários |
|-|-|
|Escalão standard do Centro de segurança do Azure de utilização | [Centro de segurança do Azure](../security-center/security-center-app-services.md) nativamente está integrado com o serviço de aplicações do Azure. Ele pode executar avaliações e fornecer recomendações de segurança. |

## <a name="next-steps"></a>Passos Seguintes

Consulte o fornecedor da aplicação para ver se existem requisitos de segurança adicionais. Para obter mais informações sobre como desenvolver aplicativos seguros, consulte [documentação de desenvolvimento seguro](../security/abstract-develop-secure-apps.md).
