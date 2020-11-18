---
title: Autenticação - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Saiba mais sobre a mitigação da autenticação na Ferramenta de Modelação de Ameaças. Consulte informações de mitigação e veja exemplos de código.
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
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: ee4dd70faab9ed44b1aa6ca8ca0ec517c7746f66
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832535"
---
# <a name="security-frame-authentication--mitigations"></a>Quadro de Segurança: Autenticação / Mitigações

| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicação Web**    | <ul><li>[Considere usar um mecanismo de autenticação padrão para autenticar a Aplicação Web](#standard-authn-web-app)</li><li>[As aplicações devem lidar com cenários de autenticação falhados de forma segura](#handle-failed-authn)</li><li>[Ativar a autenticação de step up ou adaptativa](#step-up-adaptive-authn)</li><li>[Certifique-se de que as interfaces administrativas estão devidamente bloqueadas](#admin-interface-lockdown)</li><li>[Implementar funcionalidades de palavra-passe esquecidas de forma segura](#forgot-pword-fxn)</li><li>[Certifique-se de que a política de palavra-passe e conta são implementadas](#pword-account-policy)</li><li>[Implementar controlos para evitar a enumeração do nome de utilizador](#controls-username-enum)</li></ul> |
| **Base de dados** | <ul><li>[Quando possível, utilize a autenticação do Windows para ligar ao SQL Server](#win-authn-sql)</li><li>[Quando possível utilizar autenticação de diretório ativo Azure para ligação à base de dados SQL](#aad-authn-sql)</li><li>[Quando o modo de autenticação SQL for utilizado, certifique-se de que a política de conta e palavra-passe é aplicada no servidor SQL](#authn-account-pword)</li><li>[Não utilize autenticação SQL em bases de dados contidas](#autn-contained-db)</li></ul> |
| **Hub de Eventos do Azure** | <ul><li>[Utilização de credenciais de autenticação por dispositivo utilizando fichas SaS](#authn-sas-tokens)</li></ul> |
| **Fronteira da Confiança Azure** | <ul><li>[Ativar a autenticação multi-factor Azure para administradores do Azure](#multi-factor-azure-admin)</li></ul> |
| **Limite de confiança do tecido de serviço** | <ul><li>[Restringir o acesso anónimo ao Cluster de Tecidos de Serviço](#anon-access-cluster)</li><li>[Certifique-se de que o certificado cliente-a-nó do Tecido de Serviço é diferente do certificado nó-a-nó](#fabric-cn-nn)</li><li>[Utilize o AAD para autenticar clientes para atender clusters de tecidos](#aad-client-fabric)</li><li>[Certifique-se de que os certificados de tecido de serviço são obtidos a partir de uma Autoridade de Certificados aprovada (CA)](#fabric-cert-ca)</li></ul> |
| **Servidor de Identidade** | <ul><li>[Utilize cenários de autenticação padrão suportados pelo Servidor de Identidade](#standard-authn-id)</li><li>[Substitua a cache de ficha de servidor de identidade padrão com uma alternativa escalável](#override-token)</li></ul> |
| **Limite de confiança da máquina** | <ul><li>[Certifique-se de que os binários de aplicação implantados são assinados digitalmente](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Ativar a autenticação ao ligar às filas de MSMQ no WCF](#msmq-queues)</li><li>[WCF-Não definir Mensagem clienteTipialType a nenhum](#message-none)</li><li>[WCF-Não coloque o Cliente de TransporteTipo de Identificação a nenhum](#transport-none)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que as técnicas de autenticação padrão são usadas para garantir APIs web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Utilize cenários de autenticação padrão suportados pelo Azure Ative Directory](#authn-aad)</li><li>[Sobrepor-se à cache de ficha adal padrão com uma alternativa escalável](#adal-scalable)</li><li>[Certifique-se de que o TokenReplayCache é usado para evitar a repetição de fichas de autenticação ADAL](#tokenreplaycache-adal)</li><li>[Utilize bibliotecas ADAL para gerir pedidos simbólicos de clientes da OAuth2 para AAD (ou no local AD)](#adal-oauth2)</li></ul> |
| **Gateway de campo IoT** | <ul><li>[Autenticar dispositivos que se ligam ao Gateway de Campo](#authn-devices-field)</li></ul> |
| **Gateway de nuvem IoT** | <ul><li>[Certifique-se de que os dispositivos que se ligam ao Portal Cloud são autenticados](#authn-devices-cloud)</li><li>[Utilizar credenciais de autenticação por dispositivo](#authn-cred)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Certifique-se de que apenas os recipientes e bolhas necessários recebem acesso de leitura anónima](#req-containers-anon)</li><li>[Conceder acesso limitado a objetos no armazenamento Azure usando SAS ou SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Considere usar um mecanismo de autenticação padrão para autenticar a Aplicação Web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A autenticação é o processo em que uma entidade comprova a sua identidade, tipicamente através de credenciais, como um nome de utilizador e uma palavra-passe. Existem vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles estão listados abaixo:</p><ul><li>Certificados de cliente</li><li>Baseado em Windows</li><li>Formulários baseados</li><li>Federação - ADFS</li><li>Federação - Azure AD</li><li>Federação - Servidor de Identidade</li></ul><p>Considere usar um mecanismo de autenticação padrão para identificar o processo de origem</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>As aplicações devem lidar com cenários de autenticação falhados de forma segura

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>As aplicações que autenticam explicitamente os utilizadores devem lidar com cenários de autenticação falhados de forma segura. O mecanismo de autenticação deve:</p><ul><li>Negar acesso a recursos privilegiados quando a autenticação falha</li><li>Mostrar uma mensagem de erro genérica após a autenticação falhada e o acesso negado ocorre</li></ul><p>Teste para:</p><ul><li>Proteção de recursos privilegiados após logins falhados</li><li>Uma mensagem de erro genérica é exibida na autenticação falhada e no acesso a eventos negados</li><li>As contas são desativadas após um número excessivo de tentativas falhadas</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Ativar a autenticação de step up ou adaptativa

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>Verifique se o pedido tem autorização adicional (como autenticação de intensificação ou adaptação, através de autenticação multi-fatores, como o envio de OTP em SMS, e-mail etc. ou solicitação para reautorização) para que o utilizador seja desafiado antes de ter acesso a informações sensíveis. Esta regra também se aplica para fazer alterações críticas a uma conta ou ação</p><p>Isto significa também que a adaptação da autenticação tem de ser implementada de modo a que a aplicação aplique corretamente a autorização sensível ao contexto, de modo a não permitir a manipulação não autorizada através de, por exemplo, adulteração de parâmetros</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Certifique-se de que as interfaces administrativas estão devidamente bloqueadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | A primeira solução consiste em conceder acesso apenas a partir de uma determinada gama de IP de origem para a interface administrativa. Se essa solução não for possível, é sempre recomendado impor uma autenticação de step-up ou adaptativa para iniciar sessão na interface administrativa |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Implementar funcionalidades de palavra-passe esquecidas de forma segura

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A primeira coisa é verificar se a palavra-passe esquecida e outros caminhos de recuperação enviam um link, incluindo um token de ativação limitado no tempo em vez da própria palavra-passe. A autenticação adicional baseada em fichas suaves (por exemplo, token SMS, aplicações móveis nativas, etc.) também pode ser necessária antes de o link ser enviado. Em segundo lugar, não deve bloquear a conta dos utilizadores enquanto estiver em curso o processo de obtenção de uma nova palavra-passe.</p><p>Isto pode levar a um ataque de negação de serviço sempre que um intruso decide bloquear intencionalmente os utilizadores com um ataque automatizado. Em terceiro lugar, sempre que o novo pedido de senha foi definido, a mensagem que exibe deve ser generalizada de forma a evitar a enumeração do nome de utilizador. Em quarto lugar, sempre não permite o uso de senhas antigas e implementar uma política de senha forte.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Certifique-se de que a política de palavra-passe e conta são implementadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A política de palavra-passe e conta em conformidade com a política organizacional e as melhores práticas devem ser implementadas.</p><p>Para se defender contra a força bruta e a adivinhação baseada no dicionário: Deve ser implementada uma política de palavra-passe forte para garantir que os utilizadores criam uma palavra-passe complexa (por exemplo, 12 caracteres mínimos, alfanuméricos e caracteres especiais).</p><p>As políticas de bloqueio de conta podem ser implementadas da seguinte forma:</p><ul><li>**Bloqueio suave:** Esta pode ser uma boa opção para proteger os seus utilizadores contra ataques de força bruta. Por exemplo, sempre que o utilizador introduza uma palavra-passe errada três vezes a aplicação poderia bloquear a conta por um minuto, a fim de abrandar o processo de força bruta que força a sua palavra-passe tornando menos rentável para o intruso prosseguir. Se implementasse contramedidas de bloqueio rígido para este exemplo, conseguiria um "DoS" bloqueando permanentemente as contas. Em alternativa, a aplicação pode gerar um OTP (One Time Password) e enviá-la para fora da banda (via e-mail, sms, etc.) para o utilizador. Outra abordagem poderá ser a implementação do CAPTCHA após o número de tentativas falhadas.</li><li>**Bloqueio difícil:** Este tipo de bloqueio deve ser aplicado sempre que detetar um utilizador que ataca a sua aplicação e contrariá-lo através de bloquear permanentemente a sua conta até que uma equipa de resposta tenha tempo para fazer a sua perícia. Após este processo, pode decidir devolver a conta ao utilizador ou tomar mais ações legais contra eles. Este tipo de abordagem impede o intruso de penetrar ainda mais na sua aplicação e infraestrutura.</li></ul><p>Para se defender de ataques a contas padrão e previsíveis, verifique se todas as chaves e senhas são substituíveis e são geradas ou substituídas após o tempo de instalação.</p><p>Se a aplicação tiver de gerar automaticamente senhas, certifique-se de que as palavras-passe geradas são aleatórias e têm uma elevada entropia.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Implementar controlos para evitar a enumeração do nome de utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Todas as mensagens de erro devem ser generalizadas de modo a evitar a enumeração do nome de utilizador. Também por vezes não é possível evitar fugas de informação em funcionalidades como uma página de registo. Aqui você precisa usar métodos de limitação de taxas como o CAPTCHA para evitar um ataque automatizado por um intruso. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Quando possível, utilize a autenticação do Windows para ligar ao SQL Server

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | OnPrem |
| **Atributos**              | Versão SQL - Todos |
| **Referências**              | [SQL Server - Escolha um modo de autenticação](/sql/relational-databases/security/choose-an-authentication-mode) |
| **Passos** | A Autenticação do Windows utiliza o protocolo de segurança Kerberos, fornece a aplicação da política de palavra-passe no que diz respeito à validação da complexidade para senhas fortes, fornece suporte para bloqueio de conta e suporta a expiração da palavra-passe.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Quando possível utilizar autenticação de diretório ativo Azure para ligação à base de dados SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | SQL Azure |
| **Atributos**              | Versão SQL - V12 |
| **Referências**              | [Conectar-se à base de dados SQL utilizando a autenticação do diretório ativo do Azure](../../azure-sql/database/authentication-aad-overview.md) |
| **Passos** | **Versão mínima:** Azure SQL Database V12 necessário para permitir que a Base de Dados Azure SQL utilize a autenticação AAD contra o Diretório da Microsoft |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>Quando o modo de autenticação SQL for utilizado, certifique-se de que a política de conta e palavra-passe é aplicada no servidor SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Política de senha do SQL Server](/previous-versions/sql/sql-server-2012/ms161959(v=sql.110)) |
| **Passos** | Ao utilizar a autenticação do servidor SQL, são criados logins no SQL Server que não são baseados em contas de utilizador do Windows. Tanto o nome de utilizador como a palavra-passe são criados utilizando o SQL Server e armazenados no SQL Server. O SQL Server pode utilizar mecanismos de política de palavras-passe do Windows. Pode aplicar as mesmas políticas de complexidade e expiração utilizadas no Windows às palavras-passe utilizadas dentro do SQL Server. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Não utilize autenticação SQL em bases de dados contidas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | OnPrem, SQL Azure |
| **Atributos**              | Versão SQL - MSSQL2012, Versão SQL - V12 |
| **Referências**              | [Melhores práticas de segurança com bases de dados contidas](/sql/relational-databases/databases/security-best-practices-with-contained-databases) |
| **Passos** | A ausência de uma política de palavra-passe aplicada pode aumentar a probabilidade de uma credencial fraca ser estabelecida numa base de dados contida. Alavancar a autenticação do Windows. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Utilização de credenciais de autenticação por dispositivo utilizando fichas SaS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e segurança do Event Hubs](../../event-hubs/authenticate-shared-access-signature.md) |
| **Passos** | <p>O modelo de segurança Event Hubs baseia-se numa combinação de tokens de Assinatura de Acesso Partilhado (SAS) e editores de eventos. O nome da editora representa o DeviceID que recebe o token. Isto ajudaria a associar os tokens gerados com os respetivos dispositivos.</p><p>Todas as mensagens são marcadas com o criador no lado de serviço, permitindo a deteção de tentativas de falsificação de origem em carga útil. Ao autenticar dispositivos, gere um token SaS por dispositivo para uma editora única.</p>|

## <a name="enable-azure-ad-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Ativar a autenticação multi-factor Azure para administradores do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Fronteira da Confiança Azure |
| **Fase SDL**               | Implementação |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [O que é a autenticação multi-factor Azure AD?](../../active-directory/authentication/concept-mfa-howitworks.md) |
| **Passos** | <p>A autenticação multi-factor (MFA) é um método de autenticação que requer mais do que um método de verificação e adiciona uma segunda camada crítica de segurança às entradas e transações do utilizador. Funciona exigindo dois ou mais dos seguintes métodos de verificação:</p><ul><li>Algo que você sabe (tipicamente uma palavra-passe)</li><li>Algo que você tem (um dispositivo de confiança que não é facilmente duplicado, como um telefone)</li><li>Algo que és (biometria)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Restringir o acesso anónimo ao Cluster de Tecidos de Serviço

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do tecido de serviço |
| **Fase SDL**               | Implementação |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure  |
| **Referências**              | [Cenários de segurança do cluster de tecido de serviço](../../service-fabric/service-fabric-cluster-security.md) |
| **Passos** | <p>Os clusters devem ser sempre protegidos para evitar que os utilizadores não autorizados se conectem ao seu cluster, especialmente quando tem cargas de trabalho de produção em funcionamento.</p><p>Ao criar um cluster de tecido de serviço, certifique-se de que o modo de segurança está definido para "proteger" e configurar o certificado de servidor X.509 necessário. A criação de um cluster "inseguro" permitirá que qualquer utilizador anónimo se conecte ao mesmo se expor pontos finais de gestão à Internet pública.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Certifique-se de que o certificado cliente-a-nó do Tecido de Serviço é diferente do certificado nó-a-nó

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do tecido de serviço |
| **Fase SDL**               | Implementação |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure, Ambiente - Fique sozinho |
| **Referências**              | [Garantia de certificado de cliente-nó de tecido de serviço,](../../service-fabric/service-fabric-cluster-security.md#client-to-node-certificate-security) [conecte-se a um cluster seguro usando certificado de cliente](../../service-fabric/service-fabric-connect-to-secure-cluster.md) |
| **Passos** | <p>A segurança do certificado cliente-nó é configurada ao mesmo tempo que cria o cluster através do portal Azure, modelos de Gestor de Recursos ou um modelo JSON autónomo, especificando um certificado de cliente administrativo e/ou um certificado de cliente do utilizador.</p><p>Os certificados de cliente administrativo e cliente utilizador que especifique devem ser diferentes dos certificados primários e secundários que especifica para a segurança nó-a-nó.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Utilize o AAD para autenticar clientes para atender clusters de tecidos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do tecido de serviço |
| **Fase SDL**               | Implementação |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Cenários de segurança do cluster - Recomendações de Segurança](../../service-fabric/service-fabric-cluster-security.md#security-recommendations) |
| **Passos** | Os clusters em execução no Azure também podem garantir o acesso aos pontos finais de gestão usando o Azure Ative Directory (AAD), além dos certificados de cliente. Para os clusters Azure, recomenda-se que utilize a segurança AAD para autenticar clientes e certificados para segurança nó-a-nó.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Certifique-se de que os certificados de tecido de serviço são obtidos a partir de uma Autoridade de Certificados aprovada (CA)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança do tecido de serviço |
| **Fase SDL**               | Implementação |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Certificados X.509 e Tecido de Serviço](../../service-fabric/service-fabric-cluster-security.md#x509-certificates-and-service-fabric) |
| **Passos** | <p>O Service Fabric utiliza certificados de servidor X.509 para autenticar nós e clientes.</p><p>Algumas coisas importantes a ter em conta ao utilizar certificados em tecidos de serviço:</p><ul><li>Os certificados utilizados em clusters que executam cargas de trabalho de produção devem ser criados utilizando um serviço de certificados do Windows Server corretamente configurado ou obtidos a partir de uma Autoridade de Certificados aprovada (CA). A AC pode ser um CA externo aprovado ou uma infraestrutura de chaves públicas internas devidamente gerida (PKI)</li><li>Nunca utilize certificados temporários ou de teste na produção que sejam criados com ferramentas como MakeCert.exe</li><li>Pode utilizar um certificado auto-assinado, mas só deve fazê-lo para clusters de teste e não para produção</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Utilize cenários de autenticação padrão suportados pelo Servidor de Identidade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [IdentityServer3 - A Grande Imagem](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Passos** | <p>Abaixo estão as interações típicas suportadas pelo Servidor de Identidade:</p><ul><li>Os navegadores comunicam com aplicações web</li><li>As aplicações web comunicam com APIs web (às vezes por si só, às vezes em nome de um utilizador)</li><li>Aplicações baseadas no navegador comunicam com APIs web</li><li>Aplicações nativas comunicam com APIs web</li><li>Aplicações baseadas em servidores comunicam com APIs web</li><li>As APIs web comunicam com APIs web (às vezes por si só, às vezes em nome de um utilizador)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Substitua a cache de ficha de servidor de identidade padrão com uma alternativa escalável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade |
| **Fase SDL**               | Implementação |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Implementação do servidor de identidade - Caching](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | <p>O IdentityServer tem uma cache simples incorporada na memória. Embora isto seja bom para aplicações nativas de pequena escala, não escala para aplicações de nível médio e backend pelas seguintes razões:</p><ul><li>Estas aplicações são acedidas por muitos utilizadores ao mesmo tempo. Poupar todos os tokens de acesso na mesma loja cria problemas de isolamento e apresenta desafios ao operar em escala: muitos utilizadores, cada um com tantos tokens como os recursos a que a app acede em seu nome, pode significar um número enorme e operações de procura muito caras</li><li>Estas aplicações são normalmente implantadas em topologias distribuídas, onde vários nosdes devem ter acesso à mesma cache</li><li>Fichas em cache devem sobreviver a reciclagems e desativações de processos</li><li>Por todas as razões acima, ao implementar aplicações web, recomenda-se anular a cache simbólica do Servidor de Identidade padrão com uma alternativa escalável, como Azure Cache para Redis</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Certifique-se de que os binários de aplicação implantados são assinados digitalmente

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança da máquina |
| **Fase SDL**               | Implementação |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os binários de aplicação implantados são assinados digitalmente para que a integridade dos binários possa ser verificada|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Ativar a autenticação ao ligar às filas de MSMQ no WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Quadro genérico e LÍQUIDO 3 |
| **Atributos**              | N/D |
| **Referências**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)) |
| **Passos** | O programa não permite a autenticação ao ligar-se às filas msmq, um intruso pode enviar mensagens anonimamente à fila para processamento. Se a autenticação não for usada para ligar a uma fila de MSMQ usada para entregar uma mensagem a outro programa, um intruso pode enviar uma mensagem anónima que seja maliciosa.|

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do ficheiro de configuração WCF abaixo instrui o WCF a desativar a autenticação quando se conecta a uma fila DE MSMQ para entrega de mensagens.
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
Configure o MSMQ para exigir a autenticação do Domínio do Windows ou do Certificado em todos os momentos para quaisquer mensagens recebidas ou de saída.

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do ficheiro de configuração WCF abaixo instrui o WCF para permitir a autenticação do certificado ao ligar-se a uma fila DE MSMQ. O cliente é autenticado usando certificados X.509. O certificado de cliente deve estar presente na loja de certificados do servidor.
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

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF-Não definir Mensagem clienteTipialType a nenhum

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | .Net Framework 3 |
| **Atributos**              | Tipo credencial do cliente - Nenhum |
| **Referências**              | [MSDN,](/previous-versions/msp-n-p/ff648500(v=pandp.10)) [Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Passos** | A ausência de autenticação significa que todos podem aceder a este serviço. Um serviço que não autentica os seus clientes permite o acesso a todos os utilizadores. Configure o pedido de autenticação contra as credenciais do cliente. Isto pode ser feito definindo o cliente de mensagemCredentialType para Windows ou Certificate. |

### <a name="example"></a>Exemplo
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF-Não coloque o Cliente de TransporteTipo de Identificação a nenhum

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérico, .NET Framework 3 |
| **Atributos**              | Tipo credencial do cliente - Nenhum |
| **Referências**              | [MSDN,](/previous-versions/msp-n-p/ff648500(v=pandp.10)) [Fortify](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Passos** | A ausência de autenticação significa que todos podem aceder a este serviço. Um serviço que não autentica os seus clientes permite que todos os utilizadores acedam à sua funcionalidade. Configure o pedido de autenticação contra as credenciais do cliente. Isto pode ser feito definindo o cliente de transporteCredentialType para Windows ou Certificado. |

### <a name="example"></a>Exemplo
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Certifique-se de que as técnicas de autenticação padrão são usadas para garantir APIs web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação e Autorização em ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api), [Serviços de Autenticação Externa com ASP.NET API Web (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Passos** | <p>A autenticação é o processo em que uma entidade comprova a sua identidade, tipicamente através de credenciais, como um nome de utilizador e uma palavra-passe. Existem vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles estão listados abaixo:</p><ul><li>Certificados de cliente</li><li>Baseado em Windows</li><li>Formulários baseados</li><li>Federação - ADFS</li><li>Federação - Azure AD</li><li>Federação - Servidor de Identidade</li></ul><p>Os links na secção de referências fornecem detalhes de baixo nível sobre como cada um dos esquemas de autenticação pode ser implementado para garantir uma API web.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Utilize cenários de autenticação padrão suportados pelo Azure Ative Directory

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Cenários de autenticação para Azure AD](../../active-directory/develop/authentication-vs-authorization.md), [Azure Ative Directory Code Samples](../../active-directory/azuread-dev/sample-v1-code.md), [Guia do programador do Azure Ative Directory](../../active-directory/develop/index.yml) |
| **Passos** | <p>O Azure Ative Directory (Azure AD) simplifica a autenticação para os desenvolvedores, fornecendo a identidade como um serviço, com suporte para protocolos padrão da indústria, como o OAuth 2.0 e o OpenID Connect. Abaixo estão os cinco cenários de candidatura primária apoiados pela Azure AD:</p><ul><li>Web Browser para Web Application: Um utilizador precisa de iniciar sação numa aplicação web que é protegida pelo Azure AD</li><li>Aplicação de página única (SPA): Um utilizador precisa de iniciar sação numa aplicação de uma única página que seja protegida pelo Azure AD</li><li>Aplicação nativa para Web API: Uma aplicação nativa que funciona em um telefone, tablet ou PC precisa autenticar um utilizador para obter recursos de uma API web que é protegida pela Azure AD</li><li>Web Application to Web API: Uma aplicação web precisa de obter recursos de uma API web protegida pela Azure AD</li><li>Aplicação da Daemon ou Servidor para API Web: Uma aplicação daemon ou uma aplicação de servidor sem interface de utilizador web precisa de obter recursos de uma API web protegida pela Azure AD</li></ul><p>Consulte os links na secção de referências para detalhes de implementação de baixo nível</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Sobrepor-se à cache de ficha adal padrão com uma alternativa escalável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação moderna com diretório ativo Azure para aplicações web,](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) [usando Redis como cache de token ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Passos** | <p>A cache predefinida que a ADAL (Ative Directory Authentication Library) utiliza é uma cache na memória que se baseia numa loja estática, disponível em todo o processo. Embora isto funcione para aplicações nativas, não escala para aplicações de nível médio e backend pelas seguintes razões:</p><ul><li>Estas aplicações são acedidas por muitos utilizadores ao mesmo tempo. Poupar todos os tokens de acesso na mesma loja cria problemas de isolamento e apresenta desafios ao operar em escala: muitos utilizadores, cada um com tantos tokens como os recursos a que a app acede em seu nome, pode significar um número enorme e operações de procura muito caras</li><li>Estas aplicações são normalmente implantadas em topologias distribuídas, onde vários nosdes devem ter acesso à mesma cache</li><li>Fichas em cache devem sobreviver a reciclagems e desativações de processos</li></ul><p>Por todas as razões acima, ao implementar aplicações web, recomenda-se anular a cache de token ADAL padrão com uma alternativa escalável, como Azure Cache para Redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Certifique-se de que o TokenReplayCache é usado para evitar a repetição de fichas de autenticação ADAL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação moderna com diretório ativo Azure para aplicações web](/archive/blogs/microsoft_press/new-book-modern-authentication-with-azure-active-directory-for-web-applications) |
| **Passos** | <p>A propriedade TokenReplayCache permite que os desenvolvedores definam uma cache de reprodução simbólica, uma loja que pode ser usada para guardar fichas com o propósito de verificar que nenhum token pode ser usado mais de uma vez.</p><p>Esta é uma medida contra um ataque comum, o ataque de reprodução de token apto: um atacante que intercete o token enviado no sign-in pode tentar enviá-lo novamente para a app ("replay" para estabelecer uma nova sessão. Por exemplo, No fluxo de concessão de códigoS OIDC, após a autenticação bem sucedida do utilizador, é feito um pedido de ponto final "/signin-oidc" da parte que contagiosa é feito com parâmetros "id_token", "código" e "estado".</p><p>A parte contadora valida este pedido e estabelece uma nova sessão. Se um adversário capturar este pedido e o reproduzir, pode estabelecer uma sessão de sucesso e falsificar o utilizador. A presença do nonce no OpenID Connect pode limitar, mas não eliminar totalmente as circunstâncias em que o ataque pode ser decretado com sucesso. Para proteger as suas aplicações, os desenvolvedores podem fornecer uma implementação do ITokenReplayCache e atribuir uma instância ao TokenReplayCache.</p>|

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
Aqui está uma implementação da amostra da interface ITokenReplayCache. (Por favor, personalize e implemente o seu quadro de caching específico do projeto)
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
A cache implementada tem de ser referenciada nas opções OIDC através da propriedade "TokenValidationParameters" da seguinte forma.
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

Por favor, note que para testar a eficácia desta configuração, inicie sessão na aplicação protegida local do OIDC e capture o pedido para `"/signin-oidc"` o ponto final no violinista. Quando a proteção não estiver em vigor, a repetição deste pedido no violinista definirá um novo cookie de sessão. Quando o pedido for reproduzido após a adição da proteção TokenReplayCache, a aplicação lançará uma exceção da seguinte forma: `SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>Utilize bibliotecas ADAL para gerir pedidos simbólicos de clientes da OAuth2 para AAD (ou no local AD)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [ADAL](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) |
| **Passos** | <p>A Biblioteca de Autenticação AD (ADAL) permite que os desenvolvedores de aplicações do cliente autuçam facilmente os utilizadores para cloud ou on-in Ative Directy (AD) e, em seguida, obtenham fichas de acesso para garantir chamadas API.</p><p>A ADAL tem muitas funcionalidades que facilitam a autenticação para os desenvolvedores, como suporte assíncronos, uma cache simbólica configurável que armazena tokens de acesso e tokens de atualização, atualização automática de token quando um token de acesso expira e um token de atualização está disponível, e muito mais.</p><p>Ao lidar com a maior parte da complexidade, a ADAL pode ajudar um desenvolvedor a focar-se na lógica de negócio na sua aplicação e a proteger facilmente recursos sem ser um especialista em segurança. Bibliotecas separadas estão disponíveis para .NET, JavaScript (cliente e Node.js), Python, iOS, Android e Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Autenticar dispositivos que se ligam ao Gateway de Campo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo IoT |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que cada dispositivo é autenticado pelo Field Gateway antes de aceitar dados dos mesmos e antes de facilitar as comunicações a montante com o Cloud Gateway. Além disso, certifique-se de que os dispositivos se conectam com uma credencial por dispositivo para que os dispositivos individuais possam ser identificados de forma única.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Certifique-se de que os dispositivos que se ligam ao Portal Cloud são autenticados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérico, C#, Node.JS,  |
| **Atributos**              | N/A, Escolha gateway - Azure IoT Hub |
| **Referências**              | N/A, [Hub Azure IoT com .NET](../../iot-hub/quickstart-send-telemetry-dotnet.md), [Começar com hub IoT e Node JS](../../iot-hub/quickstart-send-telemetry-node.md), [Garantir IoT com SAS e certificados,](../../iot-hub/iot-hub-devguide-security.md) [repositório Git](https://github.com/Azure/azure-iot-sdks/) |
| **Passos** | <ul><li>**Genérico:** Autenticar o dispositivo utilizando a Segurança da Camada de Transporte (TLS) ou IPSec. A infraestrutura deve suportar a utilização de teclas pré-partilhadas (PSK) nos dispositivos que não conseguem lidar com a criptografia assimétrica completa. Alavancar a Ad Azure, Oauth.</li><li>**C#:** Ao criar uma instância DeviceClient, por padrão, o método Criar cria uma instância DeviceClient que utiliza o protocolo AMQP para comunicar com o IoT Hub. Para utilizar o protocolo HTTPS, substitua o método Criar que lhe permite especificar o protocolo. Se utilizar o protocolo HTTPS, também deverá adicionar o `Microsoft.AspNet.WebApi.Client` pacote NuGet ao seu projeto para incluir o `System.Net.Http.Formatting` espaço de nome.</li></ul>|

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
#### <a name="symmetric-key"></a>Symmetric key
* Criar um hub IoT em azul
* Criar uma entrada no registo de identidade do dispositivo
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
  #### <a name="sas-token"></a>Ficha SAS
* É gerado internamente ao usar a chave simétrica, mas podemos gerá-la e usá-la explicitamente também
* Defina um protocolo: `var Http = require('azure-iot-device-http').Http;`
* Crie um símbolo sas:
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
* Conecte-se usando o símbolo sas:
    ```javascript
    Client.fromSharedAccessSignature(sas, Http);
    ```
  #### <a name="certificates"></a>Certificados
* Gere um certificado X509 auto-assinado utilizando qualquer ferramenta como o OpenSSL para gerar ficheiros .cert e .key para armazenar o certificado e a chave respectivamente
* Disposição de um dispositivo que aceite ligação segura usando certificados.
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
* Ligar um dispositivo usando um certificado
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

## <a name="use-per-device-authentication-credentials"></a><a id="authn-cred"></a>Utilizar credenciais de autenticação por dispositivo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem IoT  |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Escolha gateway - Azure IoT Hub |
| **Referências**              | [Tokens de segurança Azure IoT Hub](../../iot-hub/iot-hub-devguide-security.md) |
| **Passos** | Utilize credenciais de autenticação por dispositivo utilizando fichas SaS com base na chave do dispositivo ou certificado de cliente, em vez de políticas de acesso partilhado ao nível do IoT Hub. Isto impede a reutilização de fichas de autenticação de um dispositivo ou porta de entrada de campo por outro |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Certifique-se de que apenas os recipientes e bolhas necessários recebem acesso de leitura anónima

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | Dispositivo de Armazenamento - Blob |
| **Referências**              | [Gerir acesso de leitura anónimo a contentores e bolhas,](../../storage/blobs/anonymous-read-access-configure.md) [Assinaturas de Acesso Partilhado, Parte 1: Compreender o modelo SAS](../../storage/common/storage-sas-overview.md) |
| **Passos** | <p>Por predefinição, um recipiente e quaisquer bolhas no seu interior só podem ser acedidos pelo proprietário da conta de armazenamento. Para dar aos utilizadores anónimos que leiam permissões a um contentor e às suas bolhas, pode-se definir as permissões do contentor para permitir o acesso do público. Os utilizadores anónimos podem ler bolhas dentro de um recipiente acessível ao público sem autenticar o pedido.</p><p>Os contentores fornecem as seguintes opções para gerir o acesso ao contentor:</p><ul><li>Acesso total ao público: Os dados do contentor e da bolha podem ser lidos através de pedidos anónimos. Os clientes podem enumerar bolhas dentro do contentor através de pedidos anónimos, mas não podem enumerar contentores dentro da conta de armazenamento.</li><li>O público lê o acesso apenas para bolhas: Os dados blob dentro deste recipiente podem ser lidos através de pedido anónimo, mas os dados do contentor não estão disponíveis. Os clientes não podem enumerar bolhas dentro do contentor através de pedido anónimo</li><li>Sem acesso público: Os dados do contentor e do blob só podem ser lidos pelo proprietário da conta</li></ul><p>O acesso anónimo é o melhor para cenários onde determinadas bolhas devem estar sempre disponíveis para acesso de leitura anónima. Para um controlo mais fino, pode-se criar uma assinatura de acesso partilhado, que permite delegar o acesso restrito usando diferentes permissões e durante um intervalo de tempo especificado. Certifique-se de que os contentores e bolhas, que podem potencialmente conter dados sensíveis, não recebem acesso anónimo acidentalmente</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Conceder acesso limitado a objetos no armazenamento Azure usando SAS ou SAP

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Armazenamento do Azure |
| **Fase SDL**               | Compilar |
| **Tecnologias aplicáveis** | Genérica |
| **Atributos**              | N/D |
| **Referências**              | [Assinaturas de acesso partilhado, Parte 1: Compreender o modelo SAS,](../../storage/common/storage-sas-overview.md) [Assinaturas de Acesso Partilhado, Parte 2: Criar e utilizar um SAS com armazenamento Blob](../../storage/common/storage-sas-overview.md), [Como delegar o acesso a objetos na sua conta utilizando assinaturas de acesso partilhado e políticas de acesso armazenadas](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Passos** | <p>A utilização de uma assinatura de acesso partilhado (SAS) é uma forma poderosa de conceder acesso limitado a objetos numa conta de armazenamento a outros clientes, sem ter de expor a chave de acesso à conta. O SAS é um URI que engloba nos seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com o SAS, o cliente apenas precisa passar no SAS para o construtor ou método apropriado.</p><p>Pode utilizar um SAS quando quiser fornecer acesso a recursos na sua conta de armazenamento a um cliente que não pode ser confiado à chave da conta. As chaves da sua conta de armazenamento incluem uma chave primária e secundária, ambas que concedem acesso administrativo à sua conta e todos os recursos nela. Expor qualquer uma das chaves da sua conta abre a sua conta à possibilidade de uso malicioso ou negligente. As assinaturas de acesso partilhado fornecem uma alternativa segura que permite que outros clientes leiam, escrevam e apaguem dados na sua conta de armazenamento de acordo com as permissões que concedeu, e sem necessidade da chave da conta.</p><p>Se tiver um conjunto lógico de parâmetros que são semelhantes de cada vez, usar uma Política de Acesso Armazenada (SAP) é uma ideia melhor. Uma vez que a utilização de um SAS derivado de uma Política de Acesso Armazenada dá-lhe a capacidade de revogar imediatamente esse SAS, é a melhor prática recomendada utilizar sempre as Políticas de Acesso Armazenadas quando possível.</p>|