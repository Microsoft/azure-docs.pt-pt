---
title: Autenticação-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: mitigações para ameaças expostas no Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: d7fb9fadcfac0b57fb98dc54f40ff0d2f07c6827
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728396"
---
# <a name="security-frame-authentication--mitigations"></a>Quadro de segurança: Autenticação | Mitigações 

| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicativo Web**    | <ul><li>[Considere usar um mecanismo de autenticação padrão para autenticar o aplicativo Web](#standard-authn-web-app)</li><li>[Os aplicativos devem tratar os cenários de autenticação com falha com segurança](#handle-failed-authn)</li><li>[Habilitar o Step up ou a autenticação adaptável](#step-up-adaptive-authn)</li><li>[Verifique se as interfaces administrativas estão adequadamente bloqueadas](#admin-interface-lockdown)</li><li>[Implemente funcionalidades de senha esquecida com segurança](#forgot-pword-fxn)</li><li>[Garantir que a política de conta e senha seja implementada](#pword-account-policy)</li><li>[Implementar controles para evitar a enumeração de nome de usuário](#controls-username-enum)</li></ul> |
| **Base de Dados** | <ul><li>[Quando possível, use a autenticação do Windows para se conectar ao SQL Server](#win-authn-sql)</li><li>[Quando possível, use Azure Active Directory autenticação para se conectar ao banco de dados SQL](#aad-authn-sql)</li><li>[Quando o modo de autenticação do SQL for usado, verifique se a política de conta e senha é imposta no SQL Server](#authn-account-pword)</li><li>[Não use a autenticação do SQL em bancos de dados independentes](#autn-contained-db)</li></ul> |
| **Hub de eventos do Azure** | <ul><li>[Usar credenciais de autenticação por dispositivo usando tokens SaS](#authn-sas-tokens)</li></ul> |
| **Limite de confiança do Azure** | <ul><li>[Habilitar a autenticação multifator do Azure para administradores do Azure](#multi-factor-azure-admin)</li></ul> |
| **Limite de confiança Service Fabric** | <ul><li>[Restringir o acesso anônimo ao Cluster Service Fabric](#anon-access-cluster)</li><li>[Verifique se Service Fabric certificado de cliente para nó é diferente do certificado de nó para nó](#fabric-cn-nn)</li><li>[Usar o AAD para autenticar clientes em clusters do Service Fabric](#aad-client-fabric)</li><li>[Verifique se os certificados do Service Fabric são obtidos de uma autoridade de certificação aprovada (CA)](#fabric-cert-ca)</li></ul> |
| **Servidor de identidade** | <ul><li>[Usar cenários de autenticação padrão com suporte do servidor de identidade](#standard-authn-id)</li><li>[Substituir o cache de token do servidor de identidade padrão por uma alternativa escalonável](#override-token)</li></ul> |
| **Limite de confiança do computador** | <ul><li>[Garantir que os binários do aplicativo implantado sejam assinados digitalmente](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Habilitar a autenticação ao se conectar a filas do MSMQ no WCF](#msmq-queues)</li><li>[WCF-não defina clientCredentialtype de mensagem como None](#message-none)</li><li>[WCF-não defina clientCredentialtype de transporte como None](#transport-none)</li></ul> |
| **API Web** | <ul><li>[Verifique se as técnicas de autenticação padrão são usadas para proteger as APIs da Web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Usar cenários de autenticação padrão com suporte pelo Azure Active Directory](#authn-aad)</li><li>[Substituir o cache de token ADAL padrão por uma alternativa escalonável](#adal-scalable)</li><li>[Verifique se TokenReplayCache é usado para impedir a reprodução de tokens de autenticação ADAL](#tokenreplaycache-adal)</li><li>[Usar bibliotecas ADAL para gerenciar solicitações de token de clientes OAuth2 para o AAD (ou AD local)](#adal-oauth2)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Autenticar dispositivos que se conectam ao gateway de campo](#authn-devices-field)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Verifique se os dispositivos que se conectam ao gateway de nuvem estão autenticados](#authn-devices-cloud)</li><li>[Usar credenciais de autenticação por dispositivo](#authn-cred)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Verifique se apenas os contêineres e blobs necessários recebem acesso de leitura anônimo](#req-containers-anon)</li><li>[Conceder acesso limitado a objetos no armazenamento do Azure usando SAS ou SAP](#limited-access-sas)</li></ul> |

## <a id="standard-authn-web-app"></a>Considere usar um mecanismo de autenticação padrão para autenticar o aplicativo Web

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>A autenticação é o processo em que uma entidade comprova sua identidade, normalmente por meio de credenciais, como um nome de usuário e senha. Há vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles estão listados abaixo:</p><ul><li>Certificados de cliente</li><li>Plataforma Windows</li><li>Baseados em formulários</li><li>Federação-ADFS</li><li>Federação-Azure AD</li><li>Federação-servidor de identidade</li></ul><p>Considere usar um mecanismo de autenticação padrão para identificar o processo de origem</p>|

## <a id="handle-failed-authn"></a>Os aplicativos devem tratar os cenários de autenticação com falha com segurança

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>Os aplicativos que autenticam explicitamente os usuários devem lidar com os cenários de autenticação com falha com segurança. O mecanismo de autenticação deve:</p><ul><li>Negar acesso a recursos privilegiados quando a autenticação falhar</li><li>Exibir uma mensagem de erro genérica após a falha na autenticação e ocorrerá acesso negado</li></ul><p>Teste para:</p><ul><li>Proteção de recursos privilegiados após logons com falha</li><li>Uma mensagem de erro genérica é exibida em eventos de autenticação com falha e acesso negado (s)</li><li>As contas são desabilitadas após um número excessivo de tentativas com falha</li><ul>|

## <a id="step-up-adaptive-authn"></a>Habilitar o Step up ou a autenticação adaptável

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>Verifique se o aplicativo tem autorização adicional (como em etapas ou autenticação adaptável, por meio da autenticação multifator, como enviar OTP no SMS, email, etc. ou solicitar a reautenticação) para que o usuário seja desafiado antes de receber acesso ao informações confidenciais. Essa regra também se aplica para fazer alterações críticas em uma conta ou ação</p><p>Isso também significa que a adaptação de autenticação deve ser implementada de forma que o aplicativo aplique corretamente a autorização contextual para não permitir a manipulação não autorizada por meio de, no exemplo, violação de parâmetro</p>|

## <a id="admin-interface-lockdown"></a>Verifique se as interfaces administrativas estão adequadamente bloqueadas

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | A primeira solução é conceder acesso apenas de um determinado intervalo de IP de origem para a interface administrativa. Se essa solução não fosse possível do que é sempre recomendável impor uma autenticação de etapa ou adaptável para fazer logon na interface administrativa |

## <a id="forgot-pword-fxn"></a>Implemente funcionalidades de senha esquecida com segurança

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>A primeira coisa é verificar se as senhas esquecidas e outros caminhos de recuperação enviam um link, incluindo um token de ativação de tempo limitado, em vez da própria senha. A autenticação adicional baseada em tokens Soft (por exemplo, token de SMS, aplicativos móveis nativos etc.) também pode ser necessária, antes que o link seja enviado. Em segundo lugar, você não deve bloquear a conta de usuários enquanto o processo de obtenção de uma nova senha está em andamento.</p><p>Isso pode levar a um ataque de negação de serviço sempre que um invasor decidir bloquear intencionalmente os usuários com um ataque automatizado. Em terceiro lugar, sempre que a nova solicitação de senha tiver sido definida em andamento, a mensagem que você exibir deverá ser generalizada para evitar a enumeração de nome de usuário. Em quarto lugar, sempre não permita o uso de senhas antigas e implemente uma política de senha forte.</p> |

## <a id="pword-account-policy"></a>Garantir que a política de conta e senha seja implementada

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| Detalhes | <p>A política de senha e conta em conformidade com a política organizacional e as práticas recomendadas devem ser implementadas.</p><p>Para se defender contra a força bruta e a adivinhação baseada em dicionário: A política de senha forte deve ser implementada para garantir que os usuários criem uma senha complexa (por exemplo, comprimento mínimo de 12 caracteres, caracteres alfanuméricos e especiais).</p><p>As políticas de bloqueio de conta podem ser implementadas da seguinte maneira:</p><ul><li>**Bloqueio flexível:** Essa pode ser uma boa opção para proteger seus usuários contra ataques de força bruta. Por exemplo, sempre que o usuário insere uma senha incorreta três vezes, o aplicativo pode bloquear a conta por um minuto para reduzir o processo de força bruta de sua senha, tornando-a menos lucrativa para o invasor continuar. Se você fosse implementar contramedidas de bloqueio físico para este exemplo, você obteria um "DoS" bloqueando as contas permanentemente. Como alternativa, o aplicativo pode gerar uma OTP (senha de uma vez) e enviá-la fora de banda (por email, SMS etc.) para o usuário. Outra abordagem pode ser implementar CAPTCHA depois que um número limite de tentativas com falha for atingido.</li><li>**Bloqueio rígido:** Esse tipo de bloqueio deve ser aplicado sempre que você detectar um usuário que está atacando seu aplicativo e os combater por meio de bloquear permanentemente sua conta até que uma equipe de resposta tenha tempo para fazer suas forenses. Após esse processo, você pode decidir dar ao usuário de volta sua conta ou tomar outras ações legais contra eles. Esse tipo de abordagem impede que o invasor fique mais invadindo seu aplicativo e sua infraestrutura.</li></ul><p>Para se defender contra ataques em contas previsíveis e padrão, verifique se todas as chaves e senhas são substituíveis e são geradas ou substituídas após a hora da instalação.</p><p>Se o aplicativo tiver que gerar senhas automaticamente, certifique-se de que as senhas geradas sejam aleatórias e tenham alta entropia.</p>|

## <a id="controls-username-enum"></a>Implementar controles para evitar a enumeração de nome de usuário

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Todas as mensagens de erro devem ser generalizadas para evitar a enumeração de nome de usuário. Às vezes, você não pode evitar vazamento de informações em funcionalidades como uma página de registro. Aqui você precisa usar métodos de limitação de taxa como o CAPTCHA para evitar um ataque automatizado por um invasor. |

## <a id="win-authn-sql"></a>Quando possível, use a autenticação do Windows para se conectar ao SQL Server

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | Versão do SQL-todos |
| **Referências**              | [SQL Server-escolha um modo de autenticação](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Passos** | A autenticação do Windows usa o protocolo de segurança Kerberos, fornece imposição de diretiva de senha em relação à validação de complexidade para senhas fortes, fornece suporte para bloqueio de conta e dá suporte à expiração de senha.|

## <a id="aad-authn-sql"></a>Quando possível, use Azure Active Directory autenticação para se conectar ao banco de dados SQL

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão do SQL-V12 |
| **Referências**              | [Conectando-se ao banco de dados SQL usando a autenticação Azure Active Directory](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Passos** | **Versão mínima:** Banco de dados SQL do Azure V12 necessário para permitir que o banco de dados SQL do Azure use a autenticação do AAD no diretório da Microsoft |

## <a id="authn-account-pword"></a>Quando o modo de autenticação do SQL for usado, verifique se a política de conta e senha é imposta no SQL Server

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [SQL Server política de senha](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Passos** | Ao usar a autenticação do SQL Server, os logons são criados em SQL Server que não se baseiam em contas de usuário do Windows. O nome de usuário e a senha são criados usando SQL Server e armazenados em SQL Server. SQL Server pode usar os mecanismos de política de senha do Windows. Ele pode aplicar a mesma complexidade e políticas de expiração usadas no Windows a senhas usadas dentro de SQL Server. |

## <a id="autn-contained-db"></a>Não use a autenticação do SQL em bancos de dados independentes

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Local, SQL Azure |
| **Atributos**              | Versão do SQL-MSSQL2012, versão do SQL-V12 |
| **Referências**              | [Práticas recomendadas de segurança com bancos de dados independentes](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Passos** | A ausência de uma política de senha imposta pode aumentar a probabilidade de uma credencial fraca ser estabelecida em um banco de dados independente. Aproveite a autenticação do Windows. |

## <a id="authn-sas-tokens"></a>Usar credenciais de autenticação por dispositivo usando tokens SaS

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Visão geral do modelo de segurança e autenticação dos hubs de eventos](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | <p>O modelo de segurança dos hubs de eventos baseia-se em uma combinação de tokens de assinatura de acesso compartilhado (SAS) e editores de eventos. O nome do editor representa o DeviceID que recebe o token. Isso ajudaria a associar os tokens gerados aos respectivos dispositivos.</p><p>Todas as mensagens são marcadas com originador no lado do serviço permitindo detecção de tentativas de falsificação de origem na carga. Ao autenticar dispositivos, gere um token SaS por dispositivo com escopo para um Publicador exclusivo.</p>|

## <a id="multi-factor-azure-admin"></a>Habilitar a autenticação multifator do Azure para administradores do Azure

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do Azure | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [O que é o Multi-Factor Authentication do Azure?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Passos** | <p>A autenticação multifator (MFA) é um método de autenticação que requer mais de um método de verificação e adiciona uma segunda camada crítica de segurança a entradas e transações do usuário. Ele funciona exigindo dois ou mais dos seguintes métodos de verificação:</p><ul><li>Algo que você sabe (normalmente uma senha)</li><li>Algo que você tem (um dispositivo confiável que não é facilmente duplicado, como um telefone)</li><li>Algo que você é (biometria)</li><ul>|

## <a id="anon-access-cluster"></a>Restringir o acesso anônimo ao Cluster Service Fabric

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente-Azure  |
| **Referências**              | [Cenários de segurança de cluster do Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Passos** | <p>Os clusters sempre devem ser protegidos para impedir que usuários não autorizados se conectem ao seu cluster, especialmente quando ele tem cargas de trabalho de produção em execução.</p><p>Ao criar um cluster do Service Fabric, verifique se o modo de segurança está definido como "seguro" e configure o certificado de servidor X. 509 necessário. A criação de um cluster "inseguro" permitirá que qualquer usuário anônimo se conecte a ele se ele expor pontos de extremidade de gerenciamento à Internet pública.</p>|

## <a id="fabric-cn-nn"></a>Verifique se Service Fabric certificado de cliente para nó é diferente do certificado de nó para nó

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente-Azure, ambiente-autônomo |
| **Referências**              | [Service Fabric segurança de certificado de cliente para nó](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security), [Conecte-se a um cluster seguro usando o certificado do cliente](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Passos** | <p>A segurança de certificado de cliente para nó é configurada durante a criação do cluster por meio do portal do Azure, dos modelos do Resource Manager ou de um modelo JSON autônomo, especificando um certificado de cliente de administrador e/ou um certificado de cliente de usuário.</p><p>O cliente administrador e os certificados de cliente de usuário que você especificar devem ser diferentes dos certificados primários e secundários especificados para a segurança de nó para nó.</p>|

## <a id="aad-client-fabric"></a>Usar o AAD para autenticar clientes em clusters do Service Fabric

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente-Azure |
| **Referências**              | [Cenários de segurança de cluster-recomendações de segurança](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Passos** | Os clusters em execução no Azure também podem proteger o acesso aos pontos de extremidade de gerenciamento usando o Azure Active Directory (AAD), além dos certificados de cliente. Para clusters do Azure, é recomendável que você use a segurança do AAD para autenticar clientes e certificados para segurança de nó para nó.|

## <a id="fabric-cert-ca"></a>Verifique se os certificados do Service Fabric são obtidos de uma autoridade de certificação aprovada (CA)

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança Service Fabric | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente-Azure |
| **Referências**              | [Service Fabric e certificados X. 509](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Passos** | <p>O Service Fabric usa certificados de servidor X. 509 para autenticar nós e clientes.</p><p>Algumas coisas importantes a considerar ao usar certificados em malhas de serviço:</p><ul><li>Os certificados usados em clusters que executam cargas de trabalho de produção devem ser criados usando um serviço de certificado do Windows Server configurado corretamente ou obtidos de uma autoridade de certificação (CA) aprovada. A CA pode ser uma CA externa aprovada ou uma PKI (infraestrutura de chave pública) interna gerenciada corretamente</li><li>Nunca use nenhum certificado temporário ou de teste em produção criado com ferramentas como MakeCert. exe</li><li>Você pode usar um certificado autoassinado, mas só deve fazer isso para clusters de teste e não para produção</li></ul>|

## <a id="standard-authn-id"></a>Usar cenários de autenticação padrão com suporte do servidor de identidade

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidade | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [IdentityServer3 – visão geral](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Passos** | <p>Abaixo estão as interações típicas com suporte no servidor de identidade:</p><ul><li>Navegadores se comunicam com aplicativos Web</li><li>Os aplicativos Web se comunicam com APIs Web (às vezes, por conta própria, às vezes em nome de um usuário)</li><li>Aplicativos baseados em navegador se comunicam com APIs Web</li><li>Aplicativos nativos se comunicam com APIs Web</li><li>Aplicativos baseados em servidor se comunicam com APIs Web</li><li>APIs da Web se comunicam com APIs Web (às vezes, por conta própria, às vezes em nome de um usuário)</li></ul>|

## <a id="override-token"></a>Substituir o cache de token do servidor de identidade padrão por uma alternativa escalonável

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de identidade | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Implantação do servidor de identidade-Caching](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | <p>O IdentityServer tem um cache interno de memória simples. Embora isso seja bom para aplicativos nativos de pequena escala, ele não é dimensionado para aplicativos de camada intermediária e back-end pelos seguintes motivos:</p><ul><li>Esses aplicativos são acessados por vários usuários ao mesmo tempo. Salvar todos os tokens de acesso no mesmo armazenamento cria problemas de isolamento e apresenta desafios ao operar em escala: muitos usuários, cada um com tantos tokens quanto os recursos que o aplicativo acessa em seu nome, pode significar números enormes e operações de pesquisa muito caras</li><li>Normalmente, esses aplicativos são implantados em topologias distribuídas, em que vários nós devem ter acesso ao mesmo cache</li><li>Os tokens armazenados em cache devem sobreviver a reciclagens de processo e desativações</li><li>Por todos os motivos acima, ao implementar aplicativos Web, é recomendável substituir o cache de token do servidor de identidade padrão por uma alternativa escalonável, como o cache do Azure para Redis</li></ul>|

## <a id="binaries-signed"></a>Garantir que os binários do aplicativo implantado sejam assinados digitalmente

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do computador | 
| **Fase do SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que os binários do aplicativo implantado sejam assinados digitalmente para que a integridade dos binários possa ser verificada|

## <a id="msmq-queues"></a>Habilitar a autenticação ao se conectar a filas do MSMQ no WCF

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, NET Framework 3 |
| **Atributos**              | N/A |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Passos** | O programa falha ao habilitar a autenticação ao se conectar a filas MSMQ, um invasor pode enviar mensagens anonimamente para a fila para processamento. Se a autenticação não for usada para se conectar a uma fila MSMQ usada para entregar uma mensagem a outro programa, um invasor poderá enviar uma mensagem anônima mal-intencionada.|

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do arquivo de configuração do WCF abaixo instrui o WCF a desabilitar a autenticação ao se conectar a uma fila MSMQ para entrega de mensagens.
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
Configure o MSMQ para exigir autenticação de certificado ou domínio do Windows em todos os momentos para qualquer mensagem de entrada ou saída.

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do arquivo de configuração do WCF abaixo instrui o WCF a habilitar a autenticação de certificado ao se conectar a uma fila MSMQ. O cliente é autenticado usando certificados X. 509. O certificado do cliente deve estar presente no repositório de certificados do servidor.
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

## <a id="message-none"></a>WCF-não defina clientCredentialtype de mensagem como None

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET Framework 3 |
| **Atributos**              | Tipo de credencial do cliente-nenhum |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecimento](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_message_client) |
| **Passos** | A ausência de autenticação significa que todos podem acessar esse serviço. Um serviço que não autentica seus clientes permite o acesso a todos os usuários. Configure o aplicativo para autenticar em relação às credenciais do cliente. Isso pode ser feito definindo a mensagem clientCredentialtype como Windows ou Certificate. |

### <a name="example"></a>Exemplo
```
<message clientCredentialType=""Certificate""/>
```

## <a id="transport-none"></a>WCF-não defina clientCredentialtype de transporte como None

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, .NET Framework 3 |
| **Atributos**              | Tipo de credencial do cliente-nenhum |
| **Referências**              | [Msdn](https://msdn.microsoft.com/library/ff648500.aspx), [fortalecimento](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_anonymous_transport_client) |
| **Passos** | A ausência de autenticação significa que todos podem acessar esse serviço. Um serviço que não autentica seus clientes permite que todos os usuários acessem sua funcionalidade. Configure o aplicativo para autenticar em relação às credenciais do cliente. Isso pode ser feito definindo o transporte clientCredentialtype como Windows ou Certificate. |

### <a name="example"></a>Exemplo
```
<transport clientCredentialType=""Certificate""/>
```

## <a id="authn-secure-api"></a>Verifique se as técnicas de autenticação padrão são usadas para proteger as APIs da Web

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Autenticação e autorização no ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [serviços de autenticação externa com ASP.NET Web APIC#()](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Passos** | <p>A autenticação é o processo em que uma entidade comprova sua identidade, normalmente por meio de credenciais, como um nome de usuário e senha. Há vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles estão listados abaixo:</p><ul><li>Certificados de cliente</li><li>Plataforma Windows</li><li>Baseados em formulários</li><li>Federação-ADFS</li><li>Federação-Azure AD</li><li>Federação-servidor de identidade</li></ul><p>Os links na seção de referências fornecem detalhes de baixo nível sobre como cada um dos esquemas de autenticação pode ser implementado para proteger uma API da Web.</p>|

## <a id="authn-aad"></a>Usar cenários de autenticação padrão com suporte pelo Azure Active Directory

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Cenários de autenticação do Azure ad](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/), [Azure Active Directory exemplos de código](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/), [Azure Active Directory guia do desenvolvedor](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Passos** | <p>O Azure Active Directory (AD do Azure) simplifica a autenticação para os desenvolvedores fornecendo identidade como um serviço, com suporte para protocolos padrão do setor, como OAuth 2,0 e OpenID Connect. Abaixo estão os cinco cenários de aplicativo principais com suporte do Azure AD:</p><ul><li>Navegador da Web para aplicativo Web: Um usuário precisa entrar em um aplicativo Web protegido pelo Azure AD</li><li>SPA (aplicativo de página única): Um usuário precisa entrar em um aplicativo de página única protegido pelo Azure AD</li><li>Aplicativo nativo para API da Web: Um aplicativo nativo que é executado em um telefone, Tablet ou PC precisa autenticar um usuário para obter recursos de uma API Web protegida pelo Azure AD</li><li>Aplicativo Web para API da Web: Um aplicativo Web precisa obter recursos de uma API da Web protegida pelo Azure AD</li><li>Aplicativo de daemon ou de servidor para API da Web: Um aplicativo daemon ou um aplicativo de servidor sem interface de usuário da Web precisa obter recursos de uma API Web protegida pelo Azure AD</li></ul><p>Consulte os links na seção de referências para obter detalhes de implementação de baixo nível</p>|

## <a id="adal-scalable"></a>Substituir o cache de token ADAL padrão por uma alternativa escalonável

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Autenticação moderna com Azure Active Directory para aplicativos Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/), [usando Redis como cache de token Adal](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Passos** | <p>O cache padrão que o ADAL (Biblioteca de Autenticação do Active Directory) usa é um cache na memória que se baseia em um armazenamento estático, disponível em todo o processo. Embora isso funcione para aplicativos nativos, ele não é dimensionado para aplicativos de camada intermediária e back-end pelos seguintes motivos:</p><ul><li>Esses aplicativos são acessados por vários usuários ao mesmo tempo. Salvar todos os tokens de acesso no mesmo armazenamento cria problemas de isolamento e apresenta desafios ao operar em escala: muitos usuários, cada um com tantos tokens quanto os recursos que o aplicativo acessa em seu nome, pode significar números enormes e operações de pesquisa muito caras</li><li>Normalmente, esses aplicativos são implantados em topologias distribuídas, em que vários nós devem ter acesso ao mesmo cache</li><li>Os tokens armazenados em cache devem sobreviver a reciclagens de processo e desativações</li></ul><p>Por todos os motivos acima, ao implementar aplicativos Web, é recomendável substituir o cache de token ADAL padrão por uma alternativa escalonável, como o cache do Azure para Redis.</p>|

## <a id="tokenreplaycache-adal"></a>Verifique se TokenReplayCache é usado para impedir a reprodução de tokens de autenticação ADAL

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [Autenticação moderna com o Azure Active Directory para aplicativos Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Passos** | <p>A propriedade TokenReplayCache permite que os desenvolvedores definam um cache de reprodução de token, um repositório que pode ser usado para salvar tokens com a finalidade de verificar se nenhum token pode ser usado mais de uma vez.</p><p>Essa é uma medida em relação a um ataque comum, o ataque de reprodução de tokens adequado: um invasor interceptando o token enviado na entrada pode tentar enviá-lo ao aplicativo novamente ("replay") para estabelecer uma nova sessão. Por exemplo, no fluxo de concessão de código OIDC, após a autenticação de usuário bem-sucedida, uma solicitação para o ponto de extremidade "/SignIn-oidc" da terceira parte confiável é feita com os parâmetros "id_token", "Code" e "State".</p><p>A terceira parte confiável valida essa solicitação e estabelece uma nova sessão. Se um adversário capturar essa solicitação e reproduzi-la, ele poderá estabelecer uma sessão bem-sucedida e falsificar o usuário. A presença do nonce no OpenID Connect pode limitar, mas não eliminar totalmente as circunstâncias em que o ataque pode ser imposto com êxito. Para proteger seus aplicativos, os desenvolvedores podem fornecer uma implementação de ITokenReplayCache e atribuir uma instância a TokenReplayCache.</p>|

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
Aqui está uma implementação de exemplo da interface ITokenReplayCache. (Personalize e implemente sua estrutura de cache específica do projeto)
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
O cache implementado deve ser referenciado nas opções de OIDC por meio da propriedade "TokenValidationParameters" da seguinte maneira.
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

Observe que, para testar a eficácia dessa configuração, faça logon em seu aplicativo local protegido por OIDC e Capture a solicitação ao `"/signin-oidc"` ponto de extremidade no Fiddler. Quando a proteção não estiver em vigor, repetir essa solicitação no Fiddler definirá um novo cookie de sessão. Quando a solicitação for repetida depois que a proteção TokenReplayCache for adicionada, o aplicativo gerará uma exceção da seguinte maneira:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a id="adal-oauth2"></a>Usar bibliotecas ADAL para gerenciar solicitações de token de clientes OAuth2 para o AAD (ou AD local)

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Passos** | <p>A ADAL (biblioteca de autenticação do Azure AD) permite que os desenvolvedores de aplicativos cliente autentiquem facilmente os usuários em nuvem ou no local Active Directory (AD) e, em seguida, obtenham tokens de acesso para proteger chamadas de API.</p><p>A ADAL tem muitos recursos que facilitam a autenticação para desenvolvedores, como suporte assíncrono, um cache de token configurável que armazena tokens de acesso e tokens de atualização, atualização automática de token quando um token de acesso expira e um token de atualização está disponível e cada.</p><p>Ao lidar com a maior parte da complexidade, a ADAL pode ajudar um desenvolvedor a se concentrar na lógica de negócios em seus aplicativos e proteger facilmente os recursos sem ser especialista em segurança. Bibliotecas separadas estão disponíveis para .NET, JavaScript (Client e node. js), iOS, Android e Java.</p>|

## <a id="authn-devices-field"></a>Autenticar dispositivos que se conectam ao gateway de campo

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A  |
| **Referências**              | N/A  |
| **Passos** | Certifique-se de que cada dispositivo seja autenticado pelo gateway de campo antes de aceitar dados deles e antes de facilitar a comunicação upstream com o gateway de nuvem. Além disso, verifique se os dispositivos se conectam a uma credencial por dispositivo para que os dispositivos individuais possam ser identificados exclusivamente.|

## <a id="authn-devices-cloud"></a>Verifique se os dispositivos que se conectam ao gateway de nuvem estão autenticados

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, C#, Node. js,  |
| **Atributos**              | N/A, opção de gateway – Hub IoT do Azure |
| **Referências**              | N/A, [Hub IOT do Azure com .net](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/), [introdução com o Hub IOT e o node js](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted), [protegendo a IOT com SAS e certificados](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [repositório git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Passos** | <ul><li>**Genérica** Autenticar o dispositivo usando TLS (segurança de camada de transporte) ou IPSec. A infraestrutura deve dar suporte ao uso da chave pré-compartilhada (PSK) nesses dispositivos que não podem lidar com a criptografia assimétrica completa. Aproveite o Azure AD, OAuth.</li><li>**C#:** Ao criar uma instância de DeviceClient, por padrão, o método Create cria uma instância DeviceClient que usa o protocolo AMQP para se comunicar com o Hub IoT. Para usar o protocolo HTTPS, use a substituição do método Create que permite que você especifique o protocolo. Se você usar o protocolo HTTPS, também deverá adicionar o `Microsoft.AspNet.WebApi.Client` pacote NuGet ao seu projeto para incluir o `System.Net.Http.Formatting` namespace.</li></ul>|

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
**Node.JS: Authentication**
#### <a name="symmetric-key"></a>Chave simétrica
* Criar um hub IoT no Azure
* Criar uma entrada no registro de identidade do dispositivo
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
* É gerado internamente ao usar a chave simétrica, mas também podemos gerá-lo e usá-lo explicitamente
* Definir um protocolo:`var Http = require('azure-iot-device-http').Http;`
* Criar um token SAS:
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
* Conectar usando o token SAS: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>Certificados
* Gerar um certificado X509 autoassinado usando qualquer ferramenta como OpenSSL para gerar um arquivo. cert e. Key para armazenar o certificado e a chave, respectivamente
* Provisionar um dispositivo que aceita conexão segura usando certificados.
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
* Conectar um dispositivo usando um certificado
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

## <a id="authn-cred"></a>Usar credenciais de autenticação por dispositivo

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT  | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Opção de gateway-Hub IoT do Azure |
| **Referências**              | [Tokens de segurança do Hub IoT do Azure](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Passos** | Use as credenciais de autenticação por dispositivo usando tokens SaS com base na chave do dispositivo ou no certificado do cliente, em vez de políticas de acesso compartilhado no nível do Hub IoT. Isso impede a reutilização de tokens de autenticação de um dispositivo ou gateway de campo por outro |

## <a id="req-containers-anon"></a>Verifique se apenas os contêineres e blobs necessários recebem acesso de leitura anônimo

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Storagetype-blob |
| **Referências**              | [Gerencie o acesso de leitura anônimo a contêineres e blobs](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/), [assinaturas de acesso compartilhado, parte 1: Noções básicas sobre o modelo SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Passos** | <p>Por padrão, um contêiner e todos os BLOBs dentro dele podem ser acessados somente pelo proprietário da conta de armazenamento. Para dar aos usuários anônimos permissões de leitura para um contêiner e seus BLOBs, é possível definir as permissões de contêiner para permitir acesso público. Os usuários anônimos podem ler BLOBs dentro de um contêiner publicamente acessível sem autenticar a solicitação.</p><p>Os contêineres fornecem as seguintes opções para gerenciar o acesso ao contêiner:</p><ul><li>Acesso de leitura público completo: Os dados de contêiner e BLOB podem ser lidos por solicitação anônima. Os clientes podem enumerar BLOBs dentro do contêiner por meio de solicitação anônima, mas não podem enumerar contêineres na conta de armazenamento.</li><li>Acesso de leitura público somente para BLOBs: Os dados de blob dentro desse contêiner podem ser lidos por solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar BLOBs dentro do contêiner por solicitação anônima</li><li>Sem acesso de leitura público: Os dados de contêiner e BLOB podem ser lidos somente pelo proprietário da conta</li></ul><p>O acesso anônimo é melhor para cenários em que determinados BLOBs sempre devem estar disponíveis para acesso de leitura anônimo. Para um controle mais refinado, é possível criar uma assinatura de acesso compartilhado, que permite delegar acesso restrito usando permissões diferentes e um intervalo de tempo especificado. Certifique-se de que contêineres e BLOBs, que podem conter dados confidenciais, não recebam acesso anônimo acidentalmente</p>|

## <a id="limited-access-sas"></a>Conceder acesso limitado a objetos no armazenamento do Azure usando SAS ou SAP

| Cargo                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase do SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/A |
| **Referências**              | [Assinaturas de acesso compartilhado, parte 1: Noções básicas sobre o](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)modelo [SAS, assinaturas de acesso compartilhado, parte 2: Criar e usar uma SAS com o armazenamento](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/)de BLOBs, [como delegar acesso a objetos em sua conta usando assinaturas de acesso compartilhado e políticas de acesso armazenadas](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) |
| **Passos** | <p>O uso de uma SAS (assinatura de acesso compartilhado) é uma maneira eficiente de conceder acesso limitado a objetos em uma conta de armazenamento a outros clientes, sem a necessidade de expor a chave de acesso da conta. A SAS é um URI que abrange em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar os recursos de armazenamento com a SAS, o cliente só precisa passar a SAS para o construtor ou método apropriado.</p><p>Você pode usar uma SAS quando desejar fornecer acesso a recursos em sua conta de armazenamento a um cliente que não pode ser confiável com a chave de conta. As chaves da conta de armazenamento incluem uma chave primária e secundária, que concedem acesso administrativo à sua conta e todos os recursos nela. Expor uma das chaves de sua conta abre sua conta para a possibilidade de uso mal-intencionado ou negligente. As assinaturas de acesso compartilhado fornecem uma alternativa segura que permite que outros clientes leiam, gravem e excluam dados em sua conta de armazenamento de acordo com as permissões que você concedeu e sem a necessidade da chave de conta.</p><p>Se você tiver um conjunto lógico de parâmetros que são semelhantes a cada vez, usar uma política de acesso armazenado (SAP) é uma ideia melhor. Como o uso de uma SAS derivada de uma política de acesso armazenada oferece a capacidade de revogar essa SAS imediatamente, é a prática recomendada sempre usar políticas de acesso armazenado quando possível.</p>|
