---
title: Recomendações de segurança
description: Implemente as recomendações de segurança para ajudar a cumprir as suas obrigações de segurança, conforme indicado no nosso modelo de responsabilidade partilhada. Melhore a segurança da sua aplicação.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 09dbe0fbefb8b90b4c4e1ddef57abf3b13856183
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148081"
---
# <a name="security-recommendations-for-app-service"></a>Recomendações de segurança para o Serviço de Aplicações

Este artigo contém recomendações de segurança para o Azure App Service. A implementação destas recomendações irá ajudá-lo a cumprir as suas obrigações de segurança, tal como descrito no nosso modelo de responsabilidade partilhada e melhorará a segurança geral das suas soluções web App. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do prestador de serviços, leia [a segurança da infraestrutura Azure.](../security/fundamentals/infrastructure.md)

## <a name="general"></a>Geral

| Recomendação | Comentários |
|-|-|----|
| Mantenha-se atualizado | Utilize as versões mais recentes de plataformas apoiadas, linguagens de programação, protocolos e quadros. |

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

| Recomendação | Comentários |
|-|----|
| Desativar o acesso anónimo | A menos que precise de apoiar pedidos anónimos, desative o acesso anónimo. Para obter mais informações sobre as opções de autenticação do Serviço de Aplicações Azure, consulte [autenticação e autorização no Azure App Service](overview-authentication-authorization.md).|
| Requerem autenticação | Sempre que possível, utilize o módulo de autenticação do Serviço de Aplicações em vez de escrever código para lidar com a autenticação e autorização. Ver [Autenticação e autorização no Serviço de Aplicações Azure.](overview-authentication-authorization.md) |
| Proteger recursos back-end com acesso autenticado | Pode utilizar a identidade do utilizador ou utilizar uma identidade de aplicação para autenticar num recurso de back-end. Quando optar por utilizar uma identidade de aplicação, utilize uma [identidade gerida](overview-managed-identity.md).
| Requera a autenticação do certificado do cliente | A autenticação do certificado de cliente melhora a segurança apenas permitindo ligações de clientes que possam autenticar usando certificados que fornece. |

## <a name="data-protection"></a>Proteção de dados

| Recomendação | Comentários |
|-|-|
| Redirecionar HTTP para HTTPs | Por padrão, os clientes podem ligar-se a aplicações web utilizando http ou HTTPS. Recomendamos redirecionar HTTP para HTTPs porque HTTPS utiliza o protocolo SSL/TLS para fornecer uma ligação segura, que é encriptada e autenticada. |
| Encriptar comunicação aos recursos da Azure | Quando a sua aplicação se conecta aos recursos Azure, como [a SQL Database](https://azure.microsoft.com/services/sql-database/) ou [o Azure Storage,](../storage/index.yml)a ligação permanece em Azure. Uma vez que a ligação passa pela rede partilhada em Azure, deve sempre encriptar toda a comunicação. |
| Requerer a versão TLS mais recente possível | Desde 2018 que as novas aplicações do Azure App Service utilizam o TLS 1.2. As versões mais recentes do TLS incluem melhorias de segurança em relação às versões de protocolo mais antigas. |
| Utilizar FTPS | O Serviço de Aplicações suporta tanto o FTP como o FTPS para a implementação dos seus ficheiros. Utilize FTPS em vez de FTP quando possível. Quando um ou ambos os protocolos não estiverem a ser utilizados, deve [desativá-los](deploy-ftp.md#enforce-ftps). |
| Proteger os dados das aplicações | Não guarde segredos de aplicações, tais como credenciais de base de dados, fichas API ou chaves privadas nos seus ficheiros de código ou configuração. A abordagem geralmente aceite é aceder-lhes como [variáveis ambientais](https://wikipedia.org/wiki/Environment_variable) usando o padrão padrão na sua linguagem de eleição. No Azure App Service, pode definir variáveis ambientais através de [configurações de aplicações](./configure-common.md) e [cadeias de conexão.](./configure-common.md) As definições de aplicações e as cadeias de ligação são armazenadas encriptadas no Azure. As definições da aplicação são desencriptadas apenas antes de serem injetadas na memória de processo da sua aplicação quando a aplicação começar. As chaves de encriptação são rodadas regularmente. Em alternativa, pode integrar a sua aplicação Azure App Service com [a Azure Key Vault](../key-vault/index.yml) para gestão de segredos avançados. Ao [aceder ao Cofre-Chave com uma identidade gerida,](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)a sua aplicação App Service pode aceder de forma segura aos segredos de que necessita. |

## <a name="networking"></a>Rede

| Recomendação | Comentários |
|-|-|
| Utilize restrições estáticas de IP | O Serviço de Aplicações Azure no Windows permite definir uma lista de endereços IP que podem aceder à sua aplicação. A lista permitida pode incluir endereços IP individuais ou uma gama de endereços IP definidos por uma máscara de sub-rede. Para obter mais informações, consulte [as restrições estáticas de IP do Serviço de Aplicações Azure.](app-service-ip-restrictions.md)  |
| Utilize o nível de preços isolado | Com exceção do nível de preços isolado, todos os níveis executam as suas aplicações na infraestrutura de rede partilhada no Azure App Service. O nível isolado dá-lhe um completo isolamento de rede executando as suas aplicações dentro de um ambiente dedicado [do Serviço de Aplicações.](environment/intro.md) Um ambiente de Serviço de Aplicações funciona no seu próprio caso de [Rede Virtual Azure.](../virtual-network/index.yml)|
| Utilize ligações seguras ao aceder a recursos no local | Pode utilizar [ligações Híbridas,](app-service-hybrid-connections.md) [integração de Rede Virtual](web-sites-integrate-with-vnet.md)ou [ambientes de Serviço de Aplicações](environment/intro.md) para ligar aos recursos no local. |
| Limitar a exposição ao tráfego da rede de entrada | Os grupos de segurança da rede permitem-lhe restringir o acesso à rede e controlar o número de pontos finais expostos. Para obter mais informações, consulte [Como Controlar o Tráfego de Entrada num Ambiente de Serviço de Aplicações.](environment/app-service-app-service-environment-control-inbound-traffic.md) |

## <a name="monitoring"></a>Monitorização

| Recomendação | Comentários |
|-|-|
|Use o nível padrão do Centro de Segurança Azure | [O Azure Security Center](../security-center/defender-for-app-service-introduction.md) está nativamente integrado com o Azure App Service. Pode fazer avaliações e fornecer recomendações de segurança. |

## <a name="next-steps"></a>Passos seguintes

Consulte o seu fornecedor de aplicações para ver se existem requisitos de segurança adicionais. Para obter mais informações sobre o desenvolvimento de aplicações seguras, consulte [a Documentação de Desenvolvimento Seguro](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).