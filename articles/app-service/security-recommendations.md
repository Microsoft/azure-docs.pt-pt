---
title: Recomendações de segurança
description: Implemente as recomendações de segurança para ajudar a cumprir as suas obrigações de segurança, conforme indicado no nosso modelo de responsabilidade partilhada. Melhore a segurança da sua aplicação.
author: barclayn
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e3f901fd78436b42729607d38f0ddb533f31cd30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684116"
---
# <a name="security-recommendations-for-app-service"></a>Recomendações de segurança para o Serviço de Aplicações

Este artigo contém recomendações de segurança para o Azure App Service. A implementação destas recomendações irá ajudá-lo a cumprir as suas obrigações de segurança conforme descrito no nosso modelo de responsabilidade partilhada e melhorará a segurança global das suas soluções web App. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, leia a segurança da [infraestrutura azure.](../security/fundamentals/infrastructure.md)

## <a name="general"></a>Geral

| Recomendação | Comentários |
|-|-|----|
| Mantenha-se atualizado | Utilize as versões mais recentes de plataformas suportadas, linguagens de programação, protocolos e quadros. |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

| Recomendação | Comentários |
|-|----|
| Desativar o acesso anónimo | A menos que precise apoiar pedidos anónimos, desative o acesso anónimo. Para obter mais informações sobre as opções de autenticação do Serviço de Aplicações Azure, consulte autenticação e autorização no Serviço de [Aplicações Azure](overview-authentication-authorization.md).|
| Exigir autenticação | Sempre que possível, utilize o módulo de autenticação do Serviço de Aplicações em vez de escrever código para lidar com a autenticação e autorização. Consulte a Autenticação e autorização no Serviço de [Aplicações Azure](overview-authentication-authorization.md). |
| Proteja os recursos back-end com acesso autenticado | Pode utilizar a identidade do utilizador ou utilizar uma identidade de aplicação para autenticar um recurso de back-end. Quando optar por utilizar uma identidade de aplicação, utilize uma [identidade gerida](overview-managed-identity.md).
| Exigir autenticação do certificado de cliente | A autenticação do certificado de cliente melhora a segurança permitindo apenas ligações de clientes que possam autenticar usando certificados que fornece. |

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários |
|-|-|
| Redirecione http para HTTPs | Por padrão, os clientes podem ligar-se a aplicações web utilizando http ou HTTPS. Recomendamos redirecionar http para HTTPs porque HTTPS utiliza o protocolo SSL/TLS para fornecer uma ligação segura, que é encriptada e autenticada. |
| Encriptar a comunicação aos recursos do Azure | Quando a sua aplicação se conecta aos recursos do Azure, como a Base de [Dados SQL](https://azure.microsoft.com/services/sql-database/) ou o [Armazenamento Azure,](/azure/storage/)a ligação permanece no Azure. Uma vez que a ligação passa pela rede partilhada em Azure, deve sempre encriptar todas as comunicações. |
| Requeiram a mais recente versão TLS possível | Desde 2018 que as novas aplicações do Azure App Service utilizam o TLS 1.2. Versões mais recentes de TLS incluem melhorias de segurança em relação a versões de protocolo mais antigas. |
| Utilizar FTPS | O Serviço de Aplicações suporta ftp e FTPS para a implementação dos seus ficheiros. Utilize ftps em vez de FTP quando possível. Quando um ou ambos os protocolos não estiverem a ser utilizados, deve [desativá-los](deploy-ftp.md#enforce-ftps). |
| Proteger os dados das aplicações | Não guarde segredos de aplicação, tais como credenciais de base de dados, fichas API ou chaves privadas no seu código ou ficheiros de configuração. A abordagem geralmente aceite é acessá-las como [variáveis ambientais](https://wikipedia.org/wiki/Environment_variable) usando o padrão padrão na sua linguagem de eleição. No Serviço de Aplicações Azure, pode definir variáveis ambientais através de [configurações](web-sites-configure.md) de aplicações e [cordas de ligação](web-sites-configure.md). As definições de aplicativos e as cordas de ligação são armazenadas encriptadas no Azure. As definições da aplicação só são desencriptadas antes de serem injetadas na memória de processo da sua aplicação quando a aplicação começar. As chaves de encriptação são rodadas regularmente. Em alternativa, pode integrar a sua aplicação Azure App Service com [o Azure Key Vault](/azure/key-vault/) para uma gestão avançada de segredos. Ao [aceder ao Key Vault com uma identidade gerida,](../key-vault/tutorial-web-application-keyvault.md)a sua aplicação App Service pode aceder de forma segura aos segredos de que necessita. |

## <a name="networking"></a>Redes

| Recomendação | Comentários |
|-|-|
| Utilize restrições ip estáticas | O Serviço de Aplicações Azure no Windows permite definir uma lista de endereços IP que estão autorizados a aceder à sua aplicação. A lista permitida pode incluir endereços IP individuais ou uma série de endereços IP definidos por uma máscara de sub-rede. Para mais informações, consulte [as restrições de IP estáticas do Serviço de Aplicação Azure.](app-service-ip-restrictions.md)  |
| Utilize o nível de preços isolado | Com exceção do nível de preços isolado, todos os níveis executam as suas aplicações na infraestrutura de rede partilhada no Serviço de Aplicações Azure. O nível isolado dá-lhe o isolamento total da rede executando as suas aplicações dentro de um ambiente dedicado ao Serviço de [Aplicações.](environment/intro.md) Um ambiente de serviço de aplicações funciona no seu próprio exemplo de [Rede Virtual Azure](/azure/virtual-network/).|
| Utilize ligações seguras ao aceder aos recursos no local | Pode utilizar [ligações Híbridas,](app-service-hybrid-connections.md) [integração de redes virtuais](web-sites-integrate-with-vnet.md)ou ambiente de serviço de [aplicação](environment/intro.md) para se conectar aos recursos no local. |
| Limitar a exposição ao tráfego da rede de entrada | Os grupos de segurança da rede permitem-lhe restringir o acesso à rede e controlar o número de pontos finais expostos. Para mais informações, consulte Como Controlar o Tráfego de Entrada para um Ambiente de Serviço de [Aplicações](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários |
|-|-|
|Use o nível padrão do Azure Security Center | [O Azure Security Center](../security-center/security-center-app-services.md) está integrado de forma nativa com o Azure App Service. Pode fazer avaliações e fornecer recomendações de segurança. |

## <a name="next-steps"></a>Passos seguintes

Consulte o seu fornecedor de candidaturas para ver se existem requisitos de segurança adicionais. Para obter mais informações sobre o desenvolvimento de aplicações seguras, consulte documentação de [desenvolvimento seguro.](../security/fundamentals/abstract-develop-secure-apps.md)
