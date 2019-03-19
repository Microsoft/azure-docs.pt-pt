---
title: Autenticação - ferramenta de modelagem de ameaças da Microsoft - Azure | Documentos da Microsoft
description: atenuações para ameaças expostas na ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 56620dc1d3e315caa3e259715ed84a539b91356d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888592"
---
# <a name="security-frame-authentication--mitigations"></a>Quadro de segurança: Autenticação | Atenuações 

| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicação Web**    | <ul><li>[Considere a utilização de um mecanismo de autenticação padrão para autenticar a aplicação Web](#standard-authn-web-app)</li><li>[Aplicativos devem suportar cenários de falha de autenticação de forma segura](#handle-failed-authn)</li><li>[Passo de enable backup ou a autenticação adaptável](#step-up-adaptive-authn)</li><li>[Certifique-se de que as interfaces administrativas estejam adequadamente bloqueados para baixo](#admin-interface-lockdown)</li><li>[Implementar Esqueceu-se as funcionalidades de palavra-passe com segurança](#forgot-pword-fxn)</li><li>[Certifique-se de que a política de palavra-passe e conta são implementados](#pword-account-policy)</li><li>[Implemente controlos para impedir a enumeração de nome de utilizador](#controls-username-enum)</li></ul> |
| **Base de Dados** | <ul><li>[Sempre que possível, utilize a autenticação do Windows para ligar ao SQL Server](#win-authn-sql)</li><li>[Sempre que possível utilizar a autenticação do Active Directory do Azure para ligar a base de dados SQL](#aad-authn-sql)</li><li>[Quando o modo de autenticação de SQL é utilizado, certifique-se de que a diretiva de conta e palavra-passe são impostas no SQL server](#authn-account-pword)</li><li>[Não utilize autenticação do SQL em bases de dados contidas](#autn-contained-db)</li></ul> |
| **Hub de eventos do Azure** | <ul><li>[Utilizar por credenciais de autenticação do dispositivo utilizar SaS tokens](#authn-sas-tokens)</li></ul> |
| **Limite de fidedignidade do Azure** | <ul><li>[Ativar a multi-factor Authentication para os administradores do Azure](#multi-factor-azure-admin)</li></ul> |
| **Limite de fidedignidade do Service Fabric** | <ul><li>[Restringir o acesso anônimo ao Cluster do Service Fabric](#anon-access-cluster)</li><li>[Certifique-se de que o certificado de cliente para nó do Service Fabric é diferente do certificado de nó para nó](#fabric-cn-nn)</li><li>[Utilizar o AAD para autenticar clientes para clusters do service fabric](#aad-client-fabric)</li><li>[Certifique-se de que os certificados do serviço de recursos de infraestrutura são obtidos de um aprovados certificado de autoridade (CA)](#fabric-cert-ca)</li></ul> |
| **Servidor de identidades** | <ul><li>[Utilizar cenários de autenticação padrão suportados pelo servidor de identidades](#standard-authn-id)</li><li>[Substituir o cache de token do servidor de identidades padrão com uma alternativa dimensionável](#override-token)</li></ul> |
| **Limite de fidedignidade de máquina** | <ul><li>[Certifique-se de que os binários da aplicação implementada são assinados digitalmente](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Ativar a autenticação ao ligar a filas MSMQ no WCF](#msmq-queues)</li><li>[WCF – não clientCredentialType de mensagem não definido como none](#message-none)</li><li>[WCF – não clientCredentialType de transporte não definido como none](#transport-none)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a autenticação padrão técnicas são utilizadas para proteger as APIs da Web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Utilizar cenários de autenticação padrão suportados pelo Azure Active Directory](#authn-aad)</li><li>[Substituir o padrão cache de token ADAL com uma alternativa dimensionável](#adal-scalable)</li><li>[Certifique-se de que TokenReplayCache é utilizado para evitar a repetição de tokens de autenticação da ADAL](#tokenreplaycache-adal)</li><li>[Utilizar as bibliotecas ADAL para gerir pedidos de token de clientes de OAuth2 para AAD (ou AD no local)](#adal-oauth2)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Autenticar os dispositivos ligados para o Gateway de campo](#authn-devices-field)</li></ul> |
| **Gateway de Cloud da IoT** | <ul><li>[Certifique-se de que os dispositivos ligados ao gateway de Cloud são autenticados](#authn-devices-cloud)</li><li>[Utilizar credenciais de autenticação por dispositivo](#authn-cred)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Certifique-se de que são fornecidos apenas os contentores necessários e os blobs de acesso de leitura anónimo](#req-containers-anon)</li><li>[Conceder acesso limitado aos objetos no armazenamento do Azure com SAS ou SAP](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Considere a utilização de um mecanismo de autenticação padrão para autenticar a aplicação Web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>A autenticação é o processo em que uma entidade prova a sua identidade, normalmente através de credenciais, tal como um nome de utilizador e palavra-passe. Existem vários protocolos de autenticação disponíveis que podem ser considerados. Algumas delas estão listadas abaixo:</p><ul><li>Certificados de cliente</li><li>Plataforma Windows</li><li>Baseada em formulários</li><li>Federação - ADFS</li><li>Federação - Azure AD</li><li>Federação - servidor de identidades</li></ul><p>Considere a utilização de um mecanismo de autenticação padrão para identificar o processo de origem</p>|

## <a id="handle-failed-authn"></a>Aplicativos devem suportar cenários de falha de autenticação de forma segura

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>Aplicativos que explicitamente autenticar os utilizadores devem manipular os cenários de falha de autenticação em segurança. Tem do mecanismo de autenticação:</p><ul><li>Negar o acesso a recursos com privilégios quando ocorre uma falha de autenticação</li><li>Apresentar uma mensagem de erro genérica após a falha de autenticação e ocorre de acesso negado</li></ul><p>Teste para:</p><ul><li>Proteção de recursos com privilégios após inícios de sessão falhados</li><li>É apresentada uma mensagem de erro genérico na falha de autenticação e acesso negado evento (s)</li><li>As contas estão desativadas após um número excessivo de tentativas falhadas</li><ul>|

## <a id="step-up-adaptive-authn"></a>Passo de enable backup ou a autenticação adaptável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>Certifique-se de que o aplicativo tem autorização adicionais (como avançar ou autenticação adaptável, através da autenticação multifator, como o envio de OTP de SMS, e-mail, etc. ou solicitar a reautenticação) para que o utilizador é desafiado antes de lhes ser concedido acesso ao informações confidenciais. Esta regra aplica-se também para fazer alterações essenciais a uma conta ou a ação</p><p>Isso também significa que a adaptação de autenticação tem de ser implementado de forma que o aplicativo corretamente impõe autorização sensível ao contexto para não permitir uma manipulação não autorizada por meio do exemplo, violação de parâmetros</p>|

## <a id="admin-interface-lockdown"></a>Certifique-se de que as interfaces administrativas estejam adequadamente bloqueados para baixo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | A primeira solução é conceder acesso apenas a partir de um determinado intervalo de IP de origem para a interface administrativa. Se essa solução não seria possível, que é sempre recomendada para impor uma autenticação Step-up ou adaptável para iniciar sessão para a interface administrativa |

## <a id="forgot-pword-fxn"></a>Implementar Esqueceu-se as funcionalidades de palavra-passe com segurança

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>A primeira coisa que é verificar se esqueceu-se da palavra-passe e outros caminhos de recuperação enviar uma ligação, incluindo um token de ativação de tempo limitado, em vez da palavra-passe em si. Autenticação adicional, com base na configuração soft-tokens (por exemplo, aplicativos móveis SMS token, nativos, etc.) também pode ser necessária antes da ligação é enviada pela. Em segundo lugar, deve não bloquear a conta de utilizadores lidando simultaneamente e o processo de obtenção de uma nova palavra-passe está em curso.</p><p>Isto pode levar a um ataque de negação de serviço sempre que um invasor decide intencionalmente bloquear os utilizadores com um ataque automatizado. Terceiro, sempre que o novo pedido de palavra-passe foi definido em curso, a mensagem que é exibir deve ser generalizada para evitar a enumeração de nome de utilizador. Quarto, sempre não permitir a utilização de palavras-passe antigas e implementar uma política de palavra-passe segura.</p> |

## <a id="pword-account-policy"></a>Certifique-se de que a política de palavra-passe e conta são implementados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>Política de palavra-passe e a conta em conformidade com a política organizacional e práticas recomendadas deve ser implementada.</p><p>Se proteger contra força bruta e de adivinhação de dicionário com base em: Política de palavra-passe segura tem de ser implementada para garantir que os utilizadores criar palavra-passe complexa (por exemplo, comprimento mínimo de 12 carateres, carateres alfanuméricos e especiais).</p><p>Políticas de bloqueio de conta podem ser implementadas da seguinte forma:</p><ul><li>**Bloqueio de forma recuperável-out:** Isso pode ser uma boa opção para proteger os usuários contra ataques de força bruta. Por exemplo, sempre que o usuário insere uma palavra-passe errada três vezes o aplicativo pode bloquear a conta durante um minuto para diminuir o processo de bruta forçar sua senha tornando menos rentáveis para o atacante continuar. se fosse implementar as contramedidas de escalamento do bloqueio de disco rígidas para este exemplo, obteria um "Dos" bloqueando permanentemente as contas. Em alternativa, o aplicativo pode gerar uma OTP (senha de uma hora) e enviá-lo fora de banda (através do e-mail, sms, etc.) para o usuário. Outra abordagem seria implementar CAPTCHA após um número de limiar de tentativas falhadas é atingido.</li><li>**Bloqueio de disco rígido-out:** Este tipo de bloqueio deve ser aplicado sempre que detectar um usuário atacar seu aplicativo e o contador em contato com ele por meio de bloqueio permanentemente a conta da até que uma equipe de resposta tido tempo para fazer seus forenses. Após este processo que pode optar por dar ao utilizador criar uma conta ou tirar ainda mais ações legais contra em contato com ele. Este tipo de abordagem impede que o invasor penetrar ainda mais a sua aplicação e infraestrutura.</li></ul><p>Para se Defender contra ataques em predefinido e contas previsíveis, certifique-se de que todas as chaves e palavras-passe sejam substituíveis e são gerados ou substituídos após o tempo de instalação.</p><p>Se a aplicação tem de gerar automaticamente palavras-passe, certifique-se de que as palavras-passe geradas aleatórias e tem entropia elevada.</p>|

## <a id="controls-username-enum"></a>Implemente controlos para impedir a enumeração de nome de utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Todas as mensagens de erro devem ser generalizadas para evitar a enumeração de nome de utilizador. Também, por vezes, é possível evitar vazamento de funcionalidades, como a uma página de registro de informações. Aqui, tem de utilizar os métodos de limitação de velocidade de mensagens em fila, como a CAPTCHA para impedir que um ataque automatizado por um atacante. |

## <a id="win-authn-sql"></a>Sempre que possível, utilize a autenticação do Windows para ligar ao SQL Server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | Versão do SQL - todos os |
| **Referências**              | [SQL Server - escolher um modo de autenticação](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Passos** | Autenticação Windows utiliza o protocolo de segurança do Kerberos, fornece a imposição de políticas de palavra-passe em relação à validação de complexidade de palavras-passe seguras, fornece suporte para o bloqueio de conta e oferece suporte a expiração de palavra-passe.|

## <a id="aad-authn-sql"></a>Sempre que possível utilizar a autenticação do Active Directory do Azure para ligar a base de dados SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão do SQL - V12 |
| **Referências**              | [Ligar à base de dados SQL com a autenticação do Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Passos** | **Versão mínima:** V12 de base de dados SQL do Azure necessários para permitir que a base de dados do Azure SQL utilizar a autenticação do AAD com o Microsoft Directory |

## <a id="authn-account-pword"></a>Quando o modo de autenticação de SQL é utilizado, certifique-se de que a diretiva de conta e palavra-passe são impostas no SQL server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Política de palavra-passe do SQL Server](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Passos** | Ao utilizar a autenticação do SQL Server, inícios de sessão são criados no SQL Server que não se baseiam em contas de utilizador do Windows. O nome de utilizador e a palavra-passe são criadas com o SQL Server e armazenados no SQL Server. SQL Server pode usar os mecanismos de política de palavra-passe do Windows. Ele pode aplicar a mesma complexidade e a políticas de expiração usadas no Windows para palavras-passe utilizadas dentro do SQL Server. |

## <a id="autn-contained-db"></a>Não utilize autenticação do SQL em bases de dados contidas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem, SQL Azure |
| **Atributos**              | V12 de versão - MSSQL2012, versão do SQL - SQL |
| **Referências**              | [Melhores práticas de segurança com bases de dados contidas](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Passos** | A ausência de uma política de palavra-passe imposto pode aumentar a probabilidade de uma credencial fraca a ser estabelecida numa base de dados contida. Tire partido de autenticação do Windows. |

## <a id="authn-sas-tokens"></a>Utilizar por credenciais de autenticação do dispositivo utilizar SaS tokens

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Event Hubs autenticação e segurança visão geral do modelo](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | <p>O modelo de Hubs de eventos de segurança baseia-se numa combinação de tokens de assinatura de acesso partilhado (SAS) e os publicadores de eventos. O nome do publicador representa o ID do dispositivo que recebe o token. Isso ajudaria a associar os tokens gerados com os respectivos dispositivos.</p><p>Todas as mensagens são marcadas com origem no lado do serviço que permite a deteção de origem no payload tentativas de falsificação. Durante a autenticação de dispositivos, gerar um por token de SaS de dispositivo no âmbito de um publicador exclusivo.</p>|

## <a id="multi-factor-azure-admin"></a>Ativar a multi-factor Authentication para os administradores do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [O que é o Multi-Factor Authentication do Azure?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Passos** | <p>Autenticação multifator (MFA) é um método de autenticação que requer mais do que um método de verificação e adiciona uma segunda camada crítica de segurança aos inícios de sessão de utilizador e de transações. Funciona ao exige dois ou mais dos seguintes métodos de verificação:</p><ul><li>Algo que saiba (normalmente uma palavra-passe)</li><li>Algo que tenha (um dispositivo fidedigno que não seja duplicado facilmente, como um telemóvel)</li><li>Algo que seja (biometria)</li><ul>|

## <a id="anon-access-cluster"></a>Restringir o acesso anônimo ao Cluster do Service Fabric

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure  |
| **Referências**              | [Cenários de segurança de cluster do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Passos** | <p>Clusters sempre devem ser protegidos para impedir que os utilizadores não autorizados a ligar ao seu cluster, especialmente quando se tem cargas de trabalho de produção em execução no mesmo.</p><p>Ao criar um cluster do service fabric, certifique-se de que o modo de segurança está definido como "seguro" e configurar o certificado de servidor X.509 necessário. Criar um cluster do "não seguro" permitirá que qualquer usuário anônimo ligar ao mesmo se ele expõe os pontos finais de gestão para a Internet pública.</p>|

## <a id="fabric-cn-nn"></a>Certifique-se de que o certificado de cliente para nó do Service Fabric é diferente do certificado de nó para nó

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure, ambiente - autónomo |
| **Referências**              | [Segurança de certificado de cliente para nó do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [ligar a um cluster seguro com o certificado de cliente](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Passos** | <p>Configuração da segurança do certificado de cliente para nó ao criar o cluster através do portal do Azure, modelos do Resource Manager ou um modelo JSON de autónoma ao especificar um certificado de cliente administrativo e/ou um certificado de cliente do utilizador.</p><p>Os administrador cliente e o utilizador certificados de cliente que especificar devem ser diferentes do que os certificados primários e secundários que especificar para segurança de nó para nó.</p>|

## <a id="aad-client-fabric"></a>Utilizar o AAD para autenticar clientes para clusters do service fabric

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Cenários de segurança - recomendações de segurança do cluster](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Passos** | Clusters em execução no Azure também podem proteger o acesso aos pontos finais de gestão com o Azure Active Directory (AAD), para além dos certificados de cliente. Para os clusters do Azure, é recomendado que utilize a segurança do AAD para autenticar clientes e certificados para a segurança de nó para nó.|

## <a id="fabric-cert-ca"></a>Certifique-se de que os certificados do serviço de recursos de infraestrutura são obtidos de um aprovados certificado de autoridade (CA)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade do Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Certificados X.509 e o Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Passos** | <p>O Service Fabric utiliza certificados de servidor X.509 para a autenticação nós e os clientes.</p><p>Alguns aspetos importantes a considerar ao utilizar certificados nos recursos de infraestrutura do serviço:</p><ul><li>Certificados utilizados em clusters que executem cargas de trabalho de produção devem ser criados com um serviço de certificado de servidor Windows corretamente configurado ou obtidos a partir de um aprovados certificado autoridade (CA). A AC pode ser uma AC externa aprovada ou um corretamente gerenciado interno chave infraestrutura públicas (PKI)</li><li>Nunca utilize qualquer temporária ou certificados de teste em produção, que são criados com ferramentas como o MakeCert.exe</li><li>Pode utilizar um certificado autoassinado, mas deve apenas fazer isso para clusters de teste e não na produção</li></ul>|

## <a id="standard-authn-id"></a>Utilizar cenários de autenticação padrão suportados pelo servidor de identidades

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [IdentityServer3 - o panorama geral](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Passos** | <p>Seguem-se as interações típicas suportadas pelo servidor de identidades:</p><ul><li>Browsers comunicam com aplicações web</li><li>Aplicativos Web se comunicar com as APIs web (às vezes, por conta própria, às vezes, em nome de um utilizador)</li><li>Aplicativos baseados em navegador se comunicar com as APIs web</li><li>Aplicativos nativos se comunicar com as APIs web</li><li>Aplicativos baseados em servidor comunicam com as APIs web</li><li>As APIs da Web se comunicar com as APIs web (às vezes, por conta própria, às vezes, em nome de um utilizador)</li></ul>|

## <a id="override-token"></a>Substituir o cache de token do servidor de identidades padrão com uma alternativa dimensionável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidades | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Implementação de servidores de identidade - colocação em cache](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | <p>IdentityServer tem um simple cache de memória incorporado. Embora isso seja bom para aplicações nativas de pequena escala, não dimensionar para aplicações de camada e o back-end mid pelos seguintes motivos:</p><ul><li>Esses aplicativos são acessados por vários utilizadores em simultâneo. A guardar todos os tokens de acesso no mesmo arquivo cria os problemas de isolamento e apresenta desafios quando operando em escala: muitos usuários, cada um com os tokens dos recursos que a aplicação acede em seu nome, podem significar a enorme quantidade e operações de pesquisa muito dispendiosa</li><li>Esses aplicativos são normalmente implantados em topologias distribuídas, em que vários nós tem de ter acesso ao mesmo cache</li><li>Tokens em cache devem sobreviver causam a reciclagem das processo e desativações</li><li>Por motivos acima, durante a implementação de aplicações web, recomenda-se para substituir o cache de tokens do servidor de identidades de predefinido com uma alternativa dimensionável, como o Azure Cache de Redis</li></ul>|

## <a id="binaries-signed"></a>Certifique-se de que os binários da aplicação implementada são assinados digitalmente

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que binários da aplicação implementada são assinados digitalmente para que possa ser verificada a integridade dos binários do|

## <a id="msmq-queues"></a>Ativar a autenticação ao ligar a filas MSMQ no WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o .NET Framework 3 |
| **Atributos**              | N/A |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Passos** | Programa não consegue ativar a autenticação ao ligar a filas MSMQ, um invasor anonimamente pode enviar mensagens na fila para processamento. Se a autenticação não é utilizada para ligar a uma fila do MSMQ utilizada para entregar uma mensagem ao outro programa, um invasor enviasse uma mensagem com anônima maliciosa.|

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do ficheiro de configuração de WCF abaixo instrui o WCF para desativar a autenticação ao ligar a uma fila do MSMQ para entrega de mensagens.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""None"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```
Configure o MSMQ para exigir a autenticação de certificado ou de domínio do Windows em todos os momentos para todas as mensagens de entrada ou de saída.

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do ficheiro de configuração de WCF abaixo Instrua o WCF para ativar a autenticação de certificado ao ligar a uma fila do MSMQ. O cliente é autenticado utilizando certificados X.509. O certificado de cliente tem de estar presente no arquivo de certificados do servidor.
```
<bindings>
    <netMsmqBinding>
        <binding>
            <security>
                <transport msmqAuthenticationMode=""Certificate"" />
            </security>
        </binding>
    </netMsmqBinding>
</bindings>
```

## <a id="message-none"></a>WCF – não clientCredentialType de mensagem não definido como none

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | Tipo de credencial de cliente - None |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Passos** | A ausência de autenticação significa que todas as pessoas são capaz de aceder a este serviço. Um serviço que não autentica seus clientes permite o acesso a todos os utilizadores. Configure a aplicação para autenticar em relação às credenciais de cliente. Isso pode ser feito definindo a mensagem clientCredentialType para Windows ou o certificado. |

### <a name="example"></a>Exemplo
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>WCF – não clientCredentialType de transporte não definido como none

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o .NET Framework 3 |
| **Atributos**              | Tipo de credencial de cliente - None |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Passos** | A ausência de autenticação significa que todas as pessoas são capaz de aceder a este serviço. Um serviço que não autentica seus clientes permite que todos os utilizadores acedam a sua funcionalidade. Configure a aplicação para autenticar em relação às credenciais de cliente. Isso pode ser feito ao definir o transporte clientCredentialType para Windows ou o certificado. |

### <a name="example"></a>Exemplo
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Certifique-se de que a autenticação padrão técnicas são utilizadas para proteger as APIs da Web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Autenticação e autorização na ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [serviços de autenticação externo com a API Web ASP.NET (c#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Passos** | <p>A autenticação é o processo em que uma entidade prova a sua identidade, normalmente através de credenciais, tal como um nome de utilizador e palavra-passe. Existem vários protocolos de autenticação disponíveis que podem ser considerados. Algumas delas estão listadas abaixo:</p><ul><li>Certificados de cliente</li><li>Plataforma Windows</li><li>Baseada em formulários</li><li>Federação - ADFS</li><li>Federação - Azure AD</li><li>Federação - servidor de identidades</li></ul><p>Links na seção referências fornecem detalhes de baixo nível sobre como cada um dos esquemas de autenticação pode ser implementada para proteger uma API Web.</p>|

## <a id="authn-aad"></a>Utilizar cenários de autenticação padrão suportados pelo Azure Active Directory

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Cenários de autenticação do Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [exemplos de código do Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [guia para programadores do Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Passos** | <p>Azure Active Directory (Azure AD) simplifica a autenticação para os desenvolvedores ao fornecer identidade como um serviço, com suporte para protocolos de norma da indústria, como o OAuth 2.0 e OpenID Connect. Abaixo estão os cinco cenários de aplicação principal suportados pelo Azure AD:</p><ul><li>Navegador da Web para a aplicação Web: O utilizador precisa de iniciar sessão a uma aplicação web que está protegida pelo Azure AD</li><li>Aplicação de página única (SPA): O utilizador precisa de iniciar sessão na aplicação de página única que está protegida pelo Azure AD</li><li>Aplicação nativa a Web API: Um aplicativo nativo que é executado num telefone, tablet ou PC tem de autenticar um usuário para obter recursos de uma API web que está protegida pelo Azure AD</li><li>Aplicação Web a Web API: Precisa de uma aplicação web para obter recursos de uma API web protegida pelo Azure AD</li><li>O daemon de servidor ou a Web API: Um aplicativo de daemon ou um aplicativo de servidor sem interface do usuário de web tem de obter recursos de uma API web protegida pelo Azure AD</li></ul><p>Consulte os links na seção referências para obter detalhes de implementação</p>|

## <a id="adal-scalable"></a>Substituir o padrão cache de token ADAL com uma alternativa dimensionável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Autenticação moderna com o Azure Active Directory para aplicações Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [usando o Redis como cache de token da ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Passos** | <p>O cache padrão que utiliza a ADAL (Active Directory Authentication Library) é uma cache dentro da memória que se baseia num armazenamento estático, disponível em todo o processo. Embora funcione para aplicativos nativos, não dimensionar para aplicações de camada e o back-end mid pelos seguintes motivos:</p><ul><li>Esses aplicativos são acessados por vários utilizadores em simultâneo. A guardar todos os tokens de acesso no mesmo arquivo cria os problemas de isolamento e apresenta desafios quando operando em escala: muitos usuários, cada um com os tokens dos recursos que a aplicação acede em seu nome, podem significar a enorme quantidade e operações de pesquisa muito dispendiosa</li><li>Esses aplicativos são normalmente implantados em topologias distribuídas, em que vários nós tem de ter acesso ao mesmo cache</li><li>Tokens em cache devem sobreviver causam a reciclagem das processo e desativações</li></ul><p>Por motivos acima, durante a implementação de aplicações web, recomenda-se para substituir o padrão cache de token ADAL com uma alternativa dimensionável, como o Azure Cache de Redis.</p>|

## <a id="tokenreplaycache-adal"></a>Certifique-se de que TokenReplayCache é utilizado para evitar a repetição de tokens de autenticação da ADAL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [Autenticação moderna com o Azure Active Directory para aplicações Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Passos** | <p>A propriedade TokenReplayCache permite aos desenvolvedores definir uma cache de reprodução de tokens, um arquivo que pode ser usado para salvar os tokens com o objetivo de verificar que nenhum token pode ser usado mais de uma vez.</p><p>Esta é uma medida contra um ataque comum, o ataque de reprodução de tokens adequadamente chamado: um intruso intercetar o token enviado no início de sessão pode tentar enviar novamente para a aplicação ("reproduzir" ele) para estabelecer uma nova sessão. Por exemplo, o fluxo de concessão do código no OIDC, após a autenticação de utilizador com êxito, um pedido para "/ oidc de início de sessão" ponto de extremidade da entidade confiadora é feito com "id_token", "código" e "Estado" parâmetros.</p><p>A entidade confiadora valida este pedido e estabelece uma nova sessão. Se um adversário captura este pedido e replays-lo, ele pode estabelecer uma sessão com êxito e falsificar o utilizador. A presença do valor de uso único no OpenID Connect pode limitar, mas não completamente eliminar as circunstâncias em que se pode elaborado com êxito o ataque. Para proteger seus aplicativos, os desenvolvedores podem fornecer uma implementação de ITokenReplayCache e atribuir uma instância a TokenReplayCache.</p>|

### <a name="example"></a>Exemplo
```csharp
// ITokenReplayCache defined in ADAL
public interface ITokenReplayCache
{
bool TryAdd(string securityToken, DateTime expiresOn);
bool TryFind(string securityToken);
}
```

### <a name="example"></a>Exemplo
Eis um exemplo de implementação da ITokenReplayCache interface. (. Personalizar e implementar a sua estrutura de colocação em cache específicos do projeto)
```csharp
public class TokenReplayCache : ITokenReplayCache
{
    private readonly ICacheProvider cache; // Your project-specific cache provider
    public TokenReplayCache(ICacheProvider cache)
    {
        this.cache = cache;
    }
    public bool TryAdd(string securityToken, DateTime expiresOn)
    {
        if (this.cache.Get<string>(securityToken) == null)
        {
            this.cache.Set(securityToken, securityToken);
            return true;
        }
        return false;
    }
    public bool TryFind(string securityToken)
    {
        return this.cache.Get<string>(securityToken) != null;
    }
}
```
A cache implementada tem de ser referenciado nas opções de OIDC por meio da propriedade de "TokenValidationParameters" da seguinte forma.
```csharp
OpenIdConnectOptions openIdConnectOptions = new OpenIdConnectOptions
{
    AutomaticAuthenticate = true,
    ... // other configuration properties follow..
    TokenValidationParameters = new TokenValidationParameters
    {
        TokenReplayCache = new TokenReplayCache(/*Inject your cache provider*/);
    }
}
```

Tenha em atenção que para testar a eficácia nesta configuração, o início de sessão na sua aplicação protegida OIDC local e capturar o pedido para `"/signin-oidc"` ponto final no fiddler. Quando a proteção não está em vigor, reproduzir este pedido no fiddler definirá um novo cookie de sessão. Quando o pedido é reproduzido depois da proteção de TokenReplayCache é adicionada, o aplicativo lançará uma exceção da seguinte forma: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Utilizar as bibliotecas ADAL para gerir pedidos de token de clientes de OAuth2 para AAD (ou AD no local)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Passos** | <p>A autenticação do Azure AD Library (ADAL) permite que os desenvolvedores de aplicativos de cliente autenticar facilmente os utilizadores para a cloud ou no local do Active Directory (AD) e, em seguida, obter tokens de acesso para proteger as chamadas de API.</p><p>ADAL tem muitos recursos que a autenticação de forma mais fácil para os desenvolvedores, como o suporte assíncrono, uma cache de token configurável que armazena os tokens de acesso e tokens de atualização, atualização automática de token quando um token de acesso expira e um token de atualização está disponível, e mais.</p><p>Ao lidar com a maior parte da complexidade, ADAL pode ajudar um foco de desenvolvedor na lógica de negócio na respetiva aplicação e obter facilmente os recursos sem ser um especialista em segurança. Bibliotecas separadas estão disponíveis para .NET, JavaScript (cliente e node. js), iOS, Android e Java.</p>|

## <a id="authn-devices-field"></a>Autenticar os dispositivos ligados para o Gateway de campo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que cada dispositivo é autenticado pelo Gateway de campo antes de aceitar dados deles e antes de facilitar a montante comunicações com o Gateway de nuvem. Além disso, certifique-se de que os dispositivos se ligam com um por dispositivo para que os dispositivos individuais podem ser identificados exclusivamente de credenciais.|

## <a id="authn-devices-cloud"></a>Certifique-se de que os dispositivos ligados ao gateway de Cloud são autenticados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Cloud da IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Generic, C#, Node.JS,  |
| **Atributos**              | N/d, escolha de Gateway - IoT Hub do Azure |
| **Referências**              | N/d, [hub IoT do Azure com .NET](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [introdução ao IoT hub e o nó JS](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [proteção de IoT com SAS e certificados](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [repositório de Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Passos** | <ul><li>**Genérico:** Autenticar o dispositivo através do protocolo Transport Layer Security (TLS) ou IPSec. Infraestrutura deve suporta a utilização de chave pré-partilhada (PSK) nesses dispositivos que não é possível processar a criptografia assimétrica completa. Tire partido do Azure AD, Oauth.</li><li>**C#:** Ao criar uma instância de DeviceClient, por predefinição, o método Create cria uma instância de DeviceClient que utiliza o protocolo AMQP para comunicar com o IoT Hub. Para utilizar o protocolo HTTPS, utilize a substituição do método Create que permite-lhe especificar o protocolo. Se utilizar o protocolo HTTPS, deverá ainda adicionar o `Microsoft.AspNet.WebApi.Client` pacote NuGet ao seu projeto para incluir o `System.Net.Http.Formatting` espaço de nomes.</li></ul>|

### <a name="example"></a>Exemplo
```csharp
static DeviceClient deviceClient;

static string deviceKey = "{device key}";
static string iotHubUri = "{iot hub hostname}";

var messageString = "{message in string format}";
var message = new Message(Encoding.ASCII.GetBytes(messageString));

deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

await deviceClient.SendEventAsync(message);
```

### <a name="example"></a>Exemplo
**Node.JS: Autenticação**
#### <a name="symmetric-key"></a>Chave simétrica
* Criar um hub IoT no azure
* Criar uma entrada no registo de identidade de dispositivo
    ```javascript
    var device = new iothub.Device(null);
    device.deviceId = <DeviceId >
    registry.create(device, function(err, deviceInfo, res) {})
    ```
* Criar um dispositivo simulado
    ```javascript
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>SharedAccessKey=<SharedAccessKey>';
    var client = clientFromConnectionString(connectionString);
    ```
  #### <a name="sas-token"></a>Token de SAS
* Obtém geradas internamente ao utilizar a chave simétrica, mas pode gerar e utilizá-lo explicitamente também
* Defina um protocolo: `var Http = require('azure-iot-device-http').Http;`
* Crie um token sas:
    ```javascript
    resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();
    var deviceName = "<deviceName >";
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    var toSign = resourceUri + '\n' + expires;
    // using crypto
    var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
    const hmac = crypto.createHmac('sha256', decodedPassword);
    hmac.update(toSign);
    var base64signature = hmac.digest('base64');
    var base64UriEncoded = encodeURIComponent(base64signature);
    // construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "%2fdevices%2f"+deviceName+"&sig="
  + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
    ```
* Ligar com o sas token: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>Certificados
* Gerar uma X509 assinado automática de certificado usando uma ferramenta como o OpenSSL para gerar um .cert e .key os ficheiros para armazenar o certificado e a chave, respetivamente
* Aprovisione um dispositivo que aceita a ligação segura através de certificados.
    ```javascript
    var connectionString = '<connectionString>';
    var registry = iothub.Registry.fromConnectionString(connectionString);
    var deviceJSON = {deviceId:"<deviceId>",
    authentication: {
        x509Thumbprint: {
        primaryThumbprint: "<PrimaryThumbprint>",
        secondaryThumbprint: "<SecondaryThumbprint>"
        }
    }}
    var device = deviceJSON;
    registry.create(device, function (err) {});
    ```
* Ligar um dispositivo com um certificado
    ```javascript
    var Protocol = require('azure-iot-device-http').Http;
    var Client = require('azure-iot-device').Client;
    var connectionString = 'HostName=<HostName>DeviceId=<DeviceId>x509=true';
    var client = Client.fromConnectionString(connectionString, Protocol);
    var options = {
        key: fs.readFileSync('./key.pem', 'utf8'),
        cert: fs.readFileSync('./server.crt', 'utf8')
    }; 
    // Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client //transport to use x509 when connecting to IoT Hub
    client.setOptions(options);
    //call fn to execute after the connection is set up
    client.open(fn);
    ```

## <a id="authn-cred"></a>Utilizar credenciais de autenticação por dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de Cloud da IoT  | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Escolha de gateway - IoT Hub do Azure |
| **Referências**              | [Tokens de segurança do Hub IoT do Azure](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Passos** | Utilização por credenciais de autenticação do dispositivo utilizar SaS tokens com base na chave do dispositivo ou o certificado de cliente, em vez de IoT Hub-nível partilhada políticas de acesso. Isto impede a reutilização de tokens de autenticação de um gateway de campo ou dispositivo por outro |

## <a id="req-containers-anon"></a>Certifique-se de que são fornecidos apenas os contentores necessários e os blobs de acesso de leitura anónimo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | StorageType - Blob |
| **Referências**              | [Gerir o acesso de leitura anónimo a contentores e blobs](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [assinaturas de acesso partilhado, parte 1: Compreender o modelo SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Passos** | <p>Por predefinição, um contentor e os blobs dentro da mesma podem ser acedidos apenas pelo proprietário da conta de armazenamento. Para conceder aos utilizadores anónimos permissões de leitura para um contentor e respetivos blobs, um pode definir as permissões de contentor para permitir o acesso público. Utilizadores anónimos podem ler blobs num contentor acessível ao público sem autenticar o pedido.</p><p>Os contentores oferecem as seguintes opções para gerir o acesso do contentor:</p><ul><li>Acesso de leitura público completa: Dados de contentor e blob podem ser lido por meio de pedido anónimo. Os clientes podem enumerar os blobs no contentor através do pedido anónimo, mas não é possível enumerar os contentores na conta de armazenamento.</li><li>Acesso de leitura público para blobs apenas: Dados de BLOBs dentro deste contentor podem ser lidos por meio de pedido anónimo, mas os dados de contentor não estão disponíveis. Os clientes não é possível enumerar os blobs no contentor através do pedido anónimo</li><li>Sem acesso de leitura público: Dados de contentor e blob podem ser lida por apenas o proprietário da conta</li></ul><p>Acesso anónimo é melhor para cenários em que determinados blobs sempre devem estar disponíveis para acesso de leitura anónimo. Para um controle mais refinado, é possível criar uma assinatura de acesso partilhado, o que permite acesso de delegado restringido com permissões diferentes e ao longo de um intervalo de tempo especificado. Certifique-se de que contentores e blobs, que potencialmente podem conter dados confidenciais, não recebem acesso anônimo acidentalmente</p>|

## <a id="limited-access-sas"></a>Conceder acesso limitado aos objetos no armazenamento do Azure com SAS ou SAP

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/A |
| **Referências**              | [Assinaturas de acesso, parte 1 de partilhado: Compreender o modelo SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/), [assinaturas de acesso partilhado, parte 2: Criar e utilizar um SAS com armazenamento de BLOBs](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [como delegar acesso a objetos na sua conta com assinaturas de acesso partilhado e políticas de acesso armazenadas](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Passos** | <p>Utilizar assinaturas de acesso partilhado (SAS) é uma forma poderosa de conceder acesso limitado aos objetos numa conta de armazenamento para outros clientes, sem precisar expor a chave de acesso da conta. A SAS é um URI que abrange a seus parâmetros de consulta todas as informações necessárias para autenticar o acesso a um recurso de armazenamento. Para acessar recursos de armazenamento com a SAS, o cliente precisa apenas passar a SAS para o método ou construtor apropriado.</p><p>Pode utilizar uma SAS para fornecer acesso a recursos na sua conta de armazenamento para um cliente que não são de confiança com a chave de conta. As chaves de conta de armazenamento incluem tanto uma chave primária e secundária, sendo que ambas concedem acesso administrativo a sua conta e todos os recursos no mesmo. Exposição das suas chaves de conta abre-se a sua conta para a possibilidade de utilização por acidente ou maliciosa. Assinaturas de acesso partilhado são uma alternativa de segura que permite que outros clientes de ler, escrever e eliminar dados na sua conta de armazenamento, de acordo com as permissões que tenha concedido e sem necessidade da chave da conta.</p><p>Se tiver um conjunto lógico de parâmetros que são semelhantes a cada hora, utilizar uma política de acesso armazenadas (SAP) é uma idéia melhor. Como com uma SAS derivada de uma política de acesso armazenadas dá-lhe a capacidade de revogar esse SAS imediatamente, é a prática recomendada sempre usar políticas de acesso armazenado sempre que possível.</p>|
