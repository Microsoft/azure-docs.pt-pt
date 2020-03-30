---
title: Autenticação - Ferramenta de Modelação de Ameaças da Microsoft - Azure [ Microsoft Docs
description: atenuações para ameaças expostas na Ferramenta de Modelação de Ameaças
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
ms.openlocfilehash: 1bef73e6be4bdbe8828e1d20ea6e684759984627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72244640"
---
# <a name="security-frame-authentication--mitigations"></a>Quadro de Segurança: Autenticação [ Atenuações 

| Produto/Serviço | Artigo |
| --------------- | ------- |
| **Aplicação Web**    | <ul><li>[Considere usar um mecanismo de autenticação padrão para autenticar a Aplicação Web](#standard-authn-web-app)</li><li>[As aplicações devem lidar com cenários de autenticação falhados de forma segura](#handle-failed-authn)</li><li>[Ativar a autenticação de passo a passo ou adaptativa](#step-up-adaptive-authn)</li><li>[Certifique-se de que as interfaces administrativas estão devidamente bloqueadas](#admin-interface-lockdown)</li><li>[Implementar funcionalidades de senha esquecidas de forma segura](#forgot-pword-fxn)</li><li>[Certifique-se de que a política de senha e conta é implementada](#pword-account-policy)</li><li>[Implementar controlos para evitar a enumeração do nome de utilizador](#controls-username-enum)</li></ul> |
| **Base de Dados** | <ul><li>[Sempre que possível, utilize a autenticação do Windows para ligar ao Servidor SQL](#win-authn-sql)</li><li>[Quando possível, utilize a autenticação de diretório ativo Azure para a ligação à Base de Dados SQL](#aad-authn-sql)</li><li>[Quando for utilizado o modo de autenticação SQL, certifique-se de que a política de conta e palavra-passe é aplicada no servidor SQL](#authn-account-pword)</li><li>[Não utilize a autenticação SQL em bases de dados contidas](#autn-contained-db)</li></ul> |
| **Hub de Eventos do Azure** | <ul><li>[Utilize por dispositivo credenciais de autenticação utilizando tokens SaS](#authn-sas-tokens)</li></ul> |
| **Fronteira da Azure Trust** | <ul><li>[Ativar a autenticação multi-factor Azure para administradores azure](#multi-factor-azure-admin)</li></ul> |
| **Limite de confiança de tecido de serviço** | <ul><li>[Restringir o acesso anónimo ao Cluster de Tecidos de Serviço](#anon-access-cluster)</li><li>[Certifique-se de que o certificado cliente-a-nó da Fabric de Serviço é diferente do certificado de nó ao nó](#fabric-cn-nn)</li><li>[Use a AAD para autenticar clientes para servir clusters de tecidos](#aad-client-fabric)</li><li>[Certifique-se de que os certificados de tecido de serviço são obtidos a partir de uma Autoridade de Certificados Aprovada (CA)](#fabric-cert-ca)</li></ul> |
| **Servidor de Identidade** | <ul><li>[Utilize cenários de autenticação padrão suportados pelo Servidor de Identidade](#standard-authn-id)</li><li>[Substitua a cache de ficha sinuosa do Servidor de Identidade padrão com uma alternativa escalável](#override-token)</li></ul> |
| **Limite de confiança de máquina** | <ul><li>[Certifique-se de que os binários da aplicação implementada saem digitalmente assinados](#binaries-signed)</li></ul> |
| **WCF** | <ul><li>[Ativar a autenticação ao ligar-se às filas MSMQ no WCF](#msmq-queues)</li><li>[WCF-Não definir Mensagem clienteCredentialType a nenhum](#message-none)</li><li>[WCF-Não definir o cliente de transporteCredentialType a nenhum](#transport-none)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que as técnicas padrão de autenticação são usadas para proteger as APIs web](#authn-secure-api)</li></ul> |
| **Azure AD** | <ul><li>[Utilize cenários de autenticação padrão suportados pelo Diretório Ativo do Azure](#authn-aad)</li><li>[Sobrepor-se à cache de ficha adal padrão com uma alternativa escalável](#adal-scalable)</li><li>[Certifique-se de que tokenReplayCache é usado para evitar a repetição de fichas de autenticação ADAL](#tokenreplaycache-adal)</li><li>[Utilize bibliotecas ADAL para gerir pedidos simbólicos de clientes DaAuth2 para AAD (ou no local AD)](#adal-oauth2)</li></ul> |
| **Gateway de campo iot** | <ul><li>[Dispositivos autenticados que ligam ao Gateway de Campo](#authn-devices-field)</li></ul> |
| **Gateway da nuvem iot** | <ul><li>[Certifique-se de que os dispositivos que ligam ao gateway Cloud são autenticados](#authn-devices-cloud)</li><li>[Utilizar credenciais de autenticação por dispositivo](#authn-cred)</li></ul> |
| **Storage do Azure** | <ul><li>[Certifique-se de que apenas os recipientes e bolhas necessários recebem acesso de leitura anónimo](#req-containers-anon)</li><li>[Conceder acesso limitado a objetos em armazenamento Azure utilizando SAS ou SAP](#limited-access-sas)</li></ul> |

## <a name="consider-using-a-standard-authentication-mechanism-to-authenticate-to-web-application"></a><a id="standard-authn-web-app"></a>Considere usar um mecanismo de autenticação padrão para autenticar a Aplicação Web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A autenticação é o processo em que uma entidade prova a sua identidade, tipicamente através de credenciais, como um nome de utilizador e uma palavra-passe. Existem vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles estão listados abaixo:</p><ul><li>Certificados de cliente</li><li>Baseado em janelas</li><li>Formas baseadas</li><li>Federação - ADFS</li><li>Federação - Azure AD</li><li>Federação - Servidor de Identidade</li></ul><p>Considere usar um mecanismo de autenticação padrão para identificar o processo de origem</p>|

## <a name="applications-must-handle-failed-authentication-scenarios-securely"></a><a id="handle-failed-authn"></a>As aplicações devem lidar com cenários de autenticação falhados de forma segura

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>As aplicações que autenticam explicitamente os utilizadores devem lidar com cenários de autenticação falhados de forma segura. O mecanismo de autenticação deve:</p><ul><li>Negar acesso a recursos privilegiados quando a autenticação falha</li><li>Mostrar uma mensagem de erro genérica após a autenticação falhada e o acesso negado ocorre</li></ul><p>Teste para:</p><ul><li>Proteção de recursos privilegiados após logins falhados</li><li>Uma mensagem de erro genérica é exibida na autenticação falhada e no acesso a eventos negados</li><li>As contas são desativadas após um número excessivo de tentativas falhadas</li><ul>|

## <a name="enable-step-up-or-adaptive-authentication"></a><a id="step-up-adaptive-authn"></a>Ativar a autenticação de passo a passo ou adaptativa

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>Verifique se a aplicação tem autorização adicional (como a autenticação de intensificação ou adaptação, através da autenticação multifactor, como o envio de OTP em SMS, e-mail etc. ou solicitação para a reautenticação) para que o utilizador seja desafiado antes de ter acesso a acesso a informação sensível. Esta regra também se aplica para fazer alterações críticas a uma conta ou ação</p><p>Isto significa também que a adaptação da autenticação deve ser implementada de modo a que a aplicação aplique corretamente a autorização sensível ao contexto, de modo a não permitir a manipulação não autorizada por, por exemplo, adulteração de parâmetros</p>|

## <a name="ensure-that-administrative-interfaces-are-appropriately-locked-down"></a><a id="admin-interface-lockdown"></a>Certifique-se de que as interfaces administrativas estão devidamente bloqueadas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | A primeira solução é conceder acesso apenas a partir de uma determinada gama IP de origem para a interface administrativa. Se essa solução não for possível do que sempre é recomendado para impor uma autenticação step-up ou adaptativa para iniciar sessão na interface administrativa |

## <a name="implement-forgot-password-functionalities-securely"></a><a id="forgot-pword-fxn"></a>Implementar funcionalidades de senha esquecidas de forma segura

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A primeira coisa é verificar se a palavra-passe esquecida e outros caminhos de recuperação enviam um link incluindo um token de ativação limitado no tempo em vez da própria senha. A autenticação adicional baseada em tokens moles (por exemplo, token SMS, aplicações móveis nativas, etc.) também pode ser exigida antes de o link ser enviado. Em segundo lugar, não deve bloquear a conta dos utilizadores enquanto o processo de obtenção de uma nova senha está em andamento.</p><p>Isto pode levar a um ataque de Negação de serviço sempre que um intruso decide bloquear intencionalmente os utilizadores com um ataque automatizado. Em terceiro lugar, sempre que o novo pedido de palavra-passe foi definido em andamento, a mensagem que visualiza deve ser generalizada de forma a evitar a enumeração do nome de utilizador. Em quarto lugar, não permita sempre o uso de senhas antigas e implemente uma forte política de senhas.</p> |

## <a name="ensure-that-password-and-account-policy-are-implemented"></a><a id="pword-account-policy"></a>Certifique-se de que a política de senha e conta é implementada

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Detalhes | <p>A política de passwords e contas em conformidade com a política organizacional e as melhores práticas devem ser implementadas.</p><p>Para se defender contra a força bruta e a adivinhação baseada no dicionário: Deve ser implementada uma forte política de palavra-passe para garantir que os utilizadores criem senhas complexas (por exemplo, 12 caracteres comprimento mínimo, caracteres alfanuméricos e especiais).</p><p>As políticas de bloqueio da conta podem ser implementadas da seguinte forma:</p><ul><li>**Bloqueio suave:** Esta pode ser uma boa opção para proteger os seus utilizadores contra ataques de força bruta. Por exemplo, sempre que o utilizador introduza uma senha errada três vezes a aplicação pode bloquear a conta por um minuto, a fim de abrandar o processo de bruto forçando a sua palavra-passe tornando menos rentável para o intruso prosseguir. Se implementasse contramedidas de bloqueio rígido, para este exemplo, conseguiria um "DoS" bloqueando permanentemente as contas. Em alternativa, a aplicação pode gerar um OTP (One Time Password) e enviá-lo fora da banda (através de e-mail, sms, etc.) para o utilizador. Outra abordagem poderá ser a de implementar o CAPTCHA depois de se alcançar um número limiar de tentativas falhadas.</li><li>**Bloqueio duro:** Este tipo de bloqueio deve ser aplicado sempre que detetar um utilizador a atacar a sua aplicação e contrariá-los através de bloquear permanentemente a sua conta até que uma equipa de resposta tenha tempo para fazer os seus forenses. Após este processo pode decidir devolver a sua conta ao utilizador ou tomar novas ações legais contra eles. Este tipo de abordagem impede o atacante de penetrar ainda mais na sua aplicação e infraestrutura.</li></ul><p>Para se defender de ataques a contas predefinidas e previsíveis, verifique se todas as chaves e senhas são substituíveis e são geradas ou substituídas após o tempo de instalação.</p><p>Se a aplicação tiver de gerar automaticamente palavras-passe, certifique-se de que as palavras-passe geradas são aleatórias e têm alta entropia.</p>|

## <a name="implement-controls-to-prevent-username-enumeration"></a><a id="controls-username-enum"></a>Implementar controlos para evitar a enumeração do nome de utilizador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Todas as mensagens de erro devem ser generalizadas de modo a evitar a enumeração do nome de utilizador. Também, por vezes, não é possível evitar fugas de informação em funcionalidades como uma página de registo. Aqui é preciso utilizar métodos de limitação de taxas como o CAPTCHA para evitar um ataque automatizado por um intruso. |

## <a name="when-possible-use-windows-authentication-for-connecting-to-sql-server"></a><a id="win-authn-sql"></a>Sempre que possível, utilize a autenticação do Windows para ligar ao Servidor SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | OnPrem |
| **Atributos**              | Versão SQL - Tudo |
| **Referências**              | [Servidor SQL - Escolha um modo de autenticação](https://msdn.microsoft.com/library/ms144284.aspx) |
| **Passos** | O Windows Authentication utiliza o protocolo de segurança Kerberos, fornece aplicação da política de passwords no que diz respeito à validação da complexidade para senhas fortes, fornece suporte para bloqueio de conta e suporta a expiração da palavra-passe.|

## <a name="when-possible-use-azure-active-directory-authentication-for-connecting-to-sql-database"></a><a id="aad-authn-sql"></a>Quando possível, utilize a autenticação de diretório ativo Azure para a ligação à Base de Dados SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | SQL Azure |
| **Atributos**              | Versão SQL - V12 |
| **Referências**              | [Ligação à base de dados SQL utilizando autenticação de diretório ativo Azure](https://azure.microsoft.com/documentation/articles/sql-database-aad-authentication/) |
| **Passos** | **Versão mínima:** Base de dados Azure SQL V12 necessária para permitir que a Base de Dados Azure SQL utilize a autenticação AAD contra o Diretório da Microsoft |

## <a name="when-sql-authentication-mode-is-used-ensure-that-account-and-password-policy-are-enforced-on-sql-server"></a><a id="authn-account-pword"></a>Quando for utilizado o modo de autenticação SQL, certifique-se de que a política de conta e palavra-passe é aplicada no servidor SQL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Política de palavra-passe do Servidor SQL](https://technet.microsoft.com/library/ms161959(v=sql.110).aspx) |
| **Passos** | Ao utilizar a autenticação do servidor SQL, são criados logins no Servidor SQL que não são baseados nas contas de utilizador do Windows. Tanto o nome do utilizador como a palavra-passe são criados utilizando o Servidor SQL e armazenados no Servidor SQL. O SQL Server pode utilizar mecanismos de política de palavra-passe do Windows. Pode aplicar as mesmas políticas de complexidade e expiração utilizadas no Windows a palavras-passe utilizadas dentro do Servidor SQL. |

## <a name="do-not-use-sql-authentication-in-contained-databases"></a><a id="autn-contained-db"></a>Não utilize a autenticação SQL em bases de dados contidas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | OnPrem, SQL Azure |
| **Atributos**              | Versão SQL - MSSQL2012, versão SQL - V12 |
| **Referências**              | [Boas Práticas de Segurança com Bases de Dados Contidas](https://msdn.microsoft.com/library/ff929055.aspx) |
| **Passos** | A ausência de uma política de senha sanerada pode aumentar a probabilidade de uma credencial fraca ser estabelecida numa base de dados contida. Alavancar a autenticação do Windows. |

## <a name="use-per-device-authentication-credentials-using-sas-tokens"></a><a id="authn-sas-tokens"></a>Utilize por dispositivo credenciais de autenticação utilizando tokens SaS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Hub de Eventos do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Visão geral do modelo de autenticação e modelo de segurança do Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passos** | <p>O modelo de segurança Do Event Hubs baseia-se numa combinação de fichas de assinatura de acesso partilhado (SAS) e editores de eventos. O nome da editora representa o DeviceID que recebe o símbolo. Isto ajudaria a associar as fichas geradas com os respetivos dispositivos.</p><p>Todas as mensagens são marcadas com o autor do serviço, permitindo a deteção de tentativas de falsificação de origem em carga útil. Ao autenticar dispositivos, gere um token SaS por dispositivo a uma editora única.</p>|

## <a name="enable-azure-multi-factor-authentication-for-azure-administrators"></a><a id="multi-factor-azure-admin"></a>Ativar a autenticação multi-factor Azure para administradores azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Fronteira da Azure Trust | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [O que é a Multi-Factor Authentication do Azure?](https://azure.microsoft.com/documentation/articles/multi-factor-authentication/) |
| **Passos** | <p>A autenticação multifactor (MFA) é um método de autenticação que requer mais do que um método de verificação e adiciona uma segunda camada crítica de segurança aos insines e transações do utilizador. Funciona exigindo dois ou mais dos seguintes métodos de verificação:</p><ul><li>Algo que você sabe (tipicamente uma palavra-passe)</li><li>Algo que você tem (um dispositivo de confiança que não é facilmente duplicado, como um telefone)</li><li>Algo que você é (biometria)</li><ul>|

## <a name="restrict-anonymous-access-to-service-fabric-cluster"></a><a id="anon-access-cluster"></a>Restringir o acesso anónimo ao Cluster de Tecidos de Serviço

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de tecido de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure  |
| **Referências**              | [Cenários de segurança do cluster de tecido de serviço](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security) |
| **Passos** | <p>Os clusters devem ser sempre protegidos para evitar que os utilizadores não autorizados se conectem ao seu cluster, especialmente quando tem cargas de trabalho de produção a funcionar nele.</p><p>Ao criar um cluster de tecido de serviço, certifique-se de que o modo de segurança está definido para "proteger" e configurar o certificado de servidor X.509 necessário. A criação de um cluster "inseguro" permitirá que qualquer utilizador anónimo se conectem a ele se expor pontos finais de gestão à Internet pública.</p>|

## <a name="ensure-that-service-fabric-client-to-node-certificate-is-different-from-node-to-node-certificate"></a><a id="fabric-cn-nn"></a>Certifique-se de que o certificado cliente-a-nó da Fabric de Serviço é diferente do certificado de nó ao nó

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de tecido de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure, Ambiente - Fique sozinho |
| **Referências**              | Segurança de [certificado cliente-nó](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#_client-to-node-certificate-security)de tecido de serviço , [Ligue-se a um cluster seguro usando certificado de cliente](https://azure.microsoft.com/documentation/articles/service-fabric-connect-to-secure-cluster/) |
| **Passos** | <p>A segurança do certificado cliente-a-nó é configurada enquanto cria o cluster através do portal Azure, modelos de Gestor de Recursos ou um modelo JSON autónomo, especificando um certificado de cliente administrativo e/ou um certificado de cliente de utilizador.</p><p>Os certificados de cliente administrativo e cliente do utilizador que especifica devem ser diferentes dos certificados primários e secundários que especifica para a segurança nó-ao-nó.</p>|

## <a name="use-aad-to-authenticate-clients-to-service-fabric-clusters"></a><a id="aad-client-fabric"></a>Use a AAD para autenticar clientes para servir clusters de tecidos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de tecido de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [Cenários de segurança do cluster - Recomendações de Segurança](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#security-recommendations) |
| **Passos** | Os clusters em funcionamento no Azure também podem garantir o acesso aos pontos finais de gestão utilizando o Azure Ative Directory (AAD), além dos certificados de cliente. Para os clusters Azure, recomenda-se que utilize a segurança AAD para autenticar clientes e certificados para a segurança do nó ao nó.|

## <a name="ensure-that-service-fabric-certificates-are-obtained-from-an-approved-certificate-authority-ca"></a><a id="fabric-cert-ca"></a>Certifique-se de que os certificados de tecido de serviço são obtidos a partir de uma Autoridade de Certificados Aprovada (CA)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de tecido de serviço | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Ambiente - Azure |
| **Referências**              | [X.509 certificados e tecido de serviço](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/#x509-certificates-and-service-fabric) |
| **Passos** | <p>Service Fabric utiliza certificados de servidor X.509 para autenticar nós e clientes.</p><p>Algumas coisas importantes a considerar ao utilizar certificados em tecidos de serviço:</p><ul><li>Os certificados utilizados em clusters que executam cargas de trabalho de produção devem ser criados utilizando um serviço de certificado supor o Windows Server corretamente configurado ou obtido sabotado de uma Autoridade de Certificados Aprovada (CA). O CA pode ser um CA externo aprovado ou uma infraestrutura de chave pública interna (PKI) devidamente gerida</li><li>Nunca utilize certificados temporários ou de teste na produção que sejam criados com ferramentas como MakeCert.exe</li><li>Você pode usar um certificado auto-assinado, mas deve apenas fazê-lo para clusters de teste e não em produção</li></ul>|

## <a name="use-standard-authentication-scenarios-supported-by-identity-server"></a><a id="standard-authn-id"></a>Utilize cenários de autenticação padrão suportados pelo Servidor de Identidade

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [IdentityServer3 - A Grande Imagem](https://identityserver.github.io/Documentation/docsv2/overview/bigPicture.html) |
| **Passos** | <p>Abaixo estão as interações típicas suportadas pelo Servidor de Identidade:</p><ul><li>Os navegadores comunicam com aplicações web</li><li>As aplicações web comunicam com APIs web (às vezes por conta própria, às vezes em nome de um utilizador)</li><li>Aplicações baseadas no navegador comunicam com APIs web</li><li>As aplicações nativas comunicam com APIs web</li><li>Aplicações baseadas em servidores comunicam com APIs web</li><li>As APIs web comunicam com APIs web (às vezes por conta própria, às vezes em nome de um utilizador)</li></ul>|

## <a name="override-the-default-identity-server-token-cache-with-a-scalable-alternative"></a><a id="override-token"></a>Substitua a cache de ficha sinuosa do Servidor de Identidade padrão com uma alternativa escalável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Servidor de Identidade | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Implementação do Servidor de Identidade - Caching](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Passos** | <p>O IdentityServer tem uma simples cache incorporada na memória. Embora isto seja bom para aplicações nativas de pequena escala, não escala para aplicações de nível médio e backend pelas seguintes razões:</p><ul><li>Estas aplicações são acedidas por muitos utilizadores ao mesmo tempo. Salvar todos os tokens de acesso na mesma loja cria problemas de isolamento e apresenta desafios ao operar à escala: muitos utilizadores, cada um com tantos tokens quanto os recursos a que a aplicação acede em seu nome, pode significar números enormes e operações de procura muito caras</li><li>Estas aplicações são normalmente implantadas em topologias distribuídas, onde vários nós devem ter acesso à mesma cache</li><li>Fichas em cache devem sobreviver a reciclagems e desativações de processos</li><li>Por todas as razões acima referidas, ao implementar aplicações web, recomenda-se que substitua a cache simbólica do Servidor de Identidade padrão com uma alternativa escalável, como o Azure Cache for Redis.</li></ul>|

## <a name="ensure-that-deployed-applications-binaries-are-digitally-signed"></a><a id="binaries-signed"></a>Certifique-se de que os binários da aplicação implementada saem digitalmente assinados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de confiança de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os binários da aplicação implementada são assinados digitalmente para que a integridade dos binários possa ser verificada|

## <a name="enable-authentication-when-connecting-to-msmq-queues-in-wcf"></a><a id="msmq-queues"></a>Ativar a autenticação ao ligar-se às filas MSMQ no WCF

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Quadro 3 genérico e LÍQUIDO |
| **Atributos**              | N/D |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx) |
| **Passos** | O programa não permite a autenticação quando se conecta às filas MSMQ, um intruso pode enviar mensagens anonimamente para a fila para processamento. Se a autenticação não for usada para se ligar a uma fila MSMQ usada para entregar uma mensagem a outro programa, um intruso pode enviar uma mensagem anónima que é maliciosa.|

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do ficheiro de configuração WCF abaixo instrui o WCF a desativar a autenticação quando se conecta a uma fila MSMQ para entrega de mensagens.
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
Configure o MSMQ para exigir a autenticação do Domínio do Windows ou do Certificado em todos os momentos para quaisquer mensagens recebidas ou saídas.

### <a name="example"></a>Exemplo
O `<netMsmqBinding/>` elemento do ficheiro de configuração WCF abaixo instrui o WCF para ativar a autenticação do certificado quando estiver ligado a uma fila MSMQ. O cliente é autenticado usando certificados X.509. O certificado de cliente deve estar presente no certificado do servidor.
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

## <a name="wcf-do-not-set-message-clientcredentialtype-to-none"></a><a id="message-none"></a>WCF-Não definir Mensagem clienteCredentialType a nenhum

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | .Quadro líquido 3 |
| **Atributos**              | Tipo credencial do cliente - Nenhum |
| **Referências**              | [MSDN,](https://msdn.microsoft.com/library/ff648500.aspx) [Fortificar](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Passos** | A ausência de autenticação significa que todos podem aceder a este serviço. Um serviço que não autentica os seus clientes permite o acesso a todos os utilizadores. Configure a aplicação para autenticar contra as credenciais do cliente. Isto pode ser feito definindo o cliente de mensagemCredentialType para Windows ou Certificado. |

### <a name="example"></a>Exemplo
```
<message clientCredentialType=""Certificate""/>
```

## <a name="wcf-do-not-set-transport-clientcredentialtype-to-none"></a><a id="transport-none"></a>WCF-Não definir o cliente de transporteCredentialType a nenhum

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, .NET Quadro 3 |
| **Atributos**              | Tipo credencial do cliente - Nenhum |
| **Referências**              | [MSDN,](https://msdn.microsoft.com/library/ff648500.aspx) [Fortificar](https://community.microfocus.com/t5/UFT-Discussions/UFT-API-Test-with-WCF-wsHttpBinding/m-p/600927) |
| **Passos** | A ausência de autenticação significa que todos podem aceder a este serviço. Um serviço que não autentica os seus clientes permite a todos os utilizadores aceder à sua funcionalidade. Configure a aplicação para autenticar contra as credenciais do cliente. Isto pode ser feito definindo o cliente de transporteCredentialType para Windows ou Certificado. |

### <a name="example"></a>Exemplo
```
<transport clientCredentialType=""Certificate""/>
```

## <a name="ensure-that-standard-authentication-techniques-are-used-to-secure-web-apis"></a><a id="authn-secure-api"></a>Certifique-se de que as técnicas padrão de autenticação são usadas para proteger as APIs web

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação e Autorização em ASP.NET Web API,](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) [Serviços de Autenticação Externa com ASP.NET Web API (C#)](https://www.asp.net/web-api/overview/security/external-authentication-services) |
| **Passos** | <p>A autenticação é o processo em que uma entidade prova a sua identidade, tipicamente através de credenciais, como um nome de utilizador e uma palavra-passe. Existem vários protocolos de autenticação disponíveis que podem ser considerados. Alguns deles estão listados abaixo:</p><ul><li>Certificados de cliente</li><li>Baseado em janelas</li><li>Formas baseadas</li><li>Federação - ADFS</li><li>Federação - Azure AD</li><li>Federação - Servidor de Identidade</li></ul><p>As ligações na secção de referências fornecem detalhes de baixo nível sobre como cada um dos esquemas de autenticação pode ser implementado para garantir uma API Web.</p>|

## <a name="use-standard-authentication-scenarios-supported-by-azure-active-directory"></a><a id="authn-aad"></a>Utilize cenários de autenticação padrão suportados pelo Diretório Ativo do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Cenários de autenticação para AD Azure,](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/)Amostras de Código de [Diretório Ativo Azure,](https://azure.microsoft.com/documentation/articles/active-directory-code-samples/) [guia de programador de diretórios ativos azure](https://azure.microsoft.com/documentation/articles/active-directory-developers-guide/) |
| **Passos** | <p>O Azure Ative Directory (Azure AD) simplifica a autenticação para os desenvolvedores, fornecendo identidade como serviço, com suporte a protocolos padrão da indústria, como o OAuth 2.0 e o OpenID Connect. Abaixo estão os cinco cenários de candidatura primária suportados pela Azure AD:</p><ul><li>Web Browser para Aplicação Web: Um utilizador precisa de iniciar sessão numa aplicação web que é protegida pela Azure AD</li><li>Aplicação de página única (SPA): Um utilizador precisa de iniciar sessão numa aplicação de uma única página que é protegida pelo Azure AD</li><li>Aplicação nativa para Web API: Uma aplicação nativa que funciona num telefone, tablet ou PC precisa de autenticar um utilizador para obter recursos de uma API web que é protegida pela Azure AD</li><li>Aplicação Web para Web API: Uma aplicação web precisa de obter recursos de uma API web protegida pela Azure AD</li><li>Aplicação Daemon ou Servidor para Web API: Uma aplicação daemon ou uma aplicação de servidor sem interface de utilizador web precisa de obter recursos de uma API web protegida por Azure AD</li></ul><p>Consulte os links na secção de referências para obter detalhes de implementação de baixo nível</p>|

## <a name="override-the-default-adal-token-cache-with-a-scalable-alternative"></a><a id="adal-scalable"></a>Sobrepor-se à cache de ficha adal padrão com uma alternativa escalável

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação Moderna com Diretório Ativo Azure para Aplicações Web,](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) [Utilização do Redis como cache token ADAL](https://blogs.msdn.microsoft.com/mrochon/2016/09/19/using-redis-as-adal-token-cache/)  |
| **Passos** | <p>A cache padrão que a ADAL (Ative Directory Authentication Library) utiliza é uma cache na memória que se baseia numa loja estática, disponível em todo o processo. Embora isto funcione para aplicações nativas, não dimensiona para aplicações de nível médio e backend pelas seguintes razões:</p><ul><li>Estas aplicações são acedidas por muitos utilizadores ao mesmo tempo. Salvar todos os tokens de acesso na mesma loja cria problemas de isolamento e apresenta desafios ao operar à escala: muitos utilizadores, cada um com tantos tokens quanto os recursos a que a aplicação acede em seu nome, pode significar números enormes e operações de procura muito caras</li><li>Estas aplicações são normalmente implantadas em topologias distribuídas, onde vários nós devem ter acesso à mesma cache</li><li>Fichas em cache devem sobreviver a reciclagems e desativações de processos</li></ul><p>Por todas as razões acima referidas, ao implementar aplicações web, recomenda-se que substitua a cache de ficha seletiva Padrão ADAL com uma alternativa escalável, como o Azure Cache for Redis.</p>|

## <a name="ensure-that-tokenreplaycache-is-used-to-prevent-the-replay-of-adal-authentication-tokens"></a><a id="tokenreplaycache-adal"></a>Certifique-se de que tokenReplayCache é usado para evitar a repetição de fichas de autenticação ADAL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [Autenticação Moderna com Diretório Ativo Azure para Aplicações Web](https://blogs.msdn.microsoft.com/microsoft_press/2016/01/04/new-book-modern-authentication-with-azure-active-directory-for-web-applications/) |
| **Passos** | <p>A propriedade TokenReplayCache permite que os desenvolvedores definam uma cache de reprodução de token, uma loja que pode ser usada para guardar tokens com o propósito de verificar que nenhum token pode ser usado mais de uma vez.</p><p>Esta é uma medida contra um ataque comum, o aptamente chamado ataque de repetição de token: um intruso intercetando o token enviado no sign-in pode tentar enviá-lo para a app novamente ("replay it" para estabelecer uma nova sessão. Por exemplo, no fluxo de concessão de códigos oIDC, após autenticação bem sucedida do utilizador, é feito um pedido de ponto final "/signin-oidc" da parte que depende é feito com parâmetros "id_token", "código" e "estado".</p><p>O partido que confia valida este pedido e estabelece uma nova sessão. Se um adversário capturar este pedido e o reproduzir, pode estabelecer uma sessão de sucesso e falsificar o utilizador. A presença do nonce no OpenID Connect pode limitar, mas não eliminar totalmente as circunstâncias em que o ataque pode ser decretado com sucesso. Para proteger as suas aplicações, os desenvolvedores podem fornecer uma implementação do ITokenReplayCache e atribuir uma instância à TokenReplayCache.</p>|

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
Aqui está uma implementação de amostra da interface ITokenReplayCache. (Por favor, personalize e implemente o seu quadro específico de cache específico do projeto)
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
A cache implementada deve ser referenciada nas opções oIDC através da propriedade "TokenValidaÇãoParameters" da seguinte forma.
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

Por favor, note que para testar a eficácia desta configuração, inicie sessão `"/signin-oidc"` na sua aplicação protegida pelo OIDC local e capture o pedido de ponto final no violinista. Quando a proteção não estiver em vigor, reproduzir este pedido no violinista definirá um novo cookie de sessão. Quando o pedido for reproduzido após a adição da proteção TokenReplayCache, a aplicação lançará uma exceção da seguinte forma:`SecurityTokenReplayDetectedException: IDX10228: The securityToken has previously been validated, securityToken: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ1......`

## <a name="use-adal-libraries-to-manage-token-requests-from-oauth2-clients-to-aad-or-on-premises-ad"></a><a id="adal-oauth2"></a>Utilize bibliotecas ADAL para gerir pedidos simbólicos de clientes DaAuth2 para AAD (ou no local AD)

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Azure AD | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | [ADAL](https://azure.microsoft.com/documentation/articles/active-directory-authentication-libraries/) |
| **Passos** | <p>A Biblioteca de Autenticação Azure AD (ADAL) permite aos desenvolvedores de aplicações de clientes autenticar facilmente os utilizadores para cloud ou no local Ative Directory (AD) e, em seguida, obter tokens de acesso para garantir chamadas API.</p><p>A ADAL tem muitas funcionalidades que facilitam a autenticação para os desenvolvedores, como suporte assíncrono, uma cache token configurável que armazena fichas de acesso e fichas de atualização, atualização automática de fichas quando expira um token de acesso e um token de atualização está disponível, e mais.</p><p>Ao lidar com a maior parte da complexidade, a ADAL pode ajudar um desenvolvedor a focar-se na lógica do negócio na sua aplicação e a garantir facilmente recursos sem ser um especialista em segurança. Bibliotecas separadas estão disponíveis para .NET, JavaScript (cliente e Node.js), Python, iOS, Android e Java.</p>|

## <a name="authenticate-devices-connecting-to-the-field-gateway"></a><a id="authn-devices-field"></a>Dispositivos autenticados que ligam ao Gateway de Campo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo iot | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que cada dispositivo é autenticado pelo Field Gateway antes de aceitar dados dos mesmos e antes de facilitar as comunicações a montante com o Cloud Gateway. Além disso, certifique-se de que os dispositivos se conectam com uma credencial por dispositivo para que os dispositivos individuais possam ser identificados de forma única.|

## <a name="ensure-that-devices-connecting-to-cloud-gateway-are-authenticated"></a><a id="authn-devices-cloud"></a>Certifique-se de que os dispositivos que ligam ao gateway Cloud são autenticados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway da nuvem iot | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérico, C#, Node.JS,  |
| **Atributos**              | N/A, Escolha gateway - Hub Azure IoT |
| **Referências**              | N/A, [hub Azure IoT com .NET,](https://azure.microsoft.com/documentation/articles/iot-hub-csharp-csharp-getstarted/) [Getting Started with IoT hub e Node JS,](https://azure.microsoft.com/documentation/articles/iot-hub-node-node-getstarted) [Secureing IoT com SAS e certificados](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/), [Repositório Git](https://github.com/Azure/azure-iot-sdks/tree/master/node) |
| **Passos** | <ul><li>**Genérico:** Autenticar o dispositivo utilizando a Segurança da Camada de Transporte (TLS) ou IPSec. A infraestrutura deve suportar a utilização de uma chave pré-partilhada (PSK) nos dispositivos que não conseguem lidar com criptografia assimétrica completa. Alavancar a AD Azure, Oauth.</li><li>**C#:** Ao criar uma instância DeviceClient, por padrão, o método Create cria uma instância De DispositivoClient que utiliza o protocolo AMQP para comunicar com o IoT Hub. Para utilizar o protocolo HTTPS, substitua o método Criar que lhe permite especificar o protocolo. Se utilizar o protocolo HTTPS, deve `Microsoft.AspNet.WebApi.Client` também adicionar o pacote `System.Net.Http.Formatting` NuGet ao seu projeto para incluir o espaço de nome.</li></ul>|

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
**Nó.JS: Autenticação**
#### <a name="symmetric-key"></a>Symmetric key
* Criar um hub IoT no azul
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
* É gerado internamente quando se usa a chave simétrica, mas podemos gerá-la e usá-la explicitamente também
* Definir um protocolo:`var Http = require('azure-iot-device-http').Http;`
* Crie um símbolo de sas:
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
* Conecte-se usando token de sas: 
    ```javascript
    Client.fromSharedAccessSignature(sas, Http); 
    ```
  #### <a name="certificates"></a>Certificados
* Gere um certificado X509 auto-assinado utilizando qualquer ferramenta como a OpenSSL para gerar ficheiros .cert e .key para armazenar o certificado e a chave, respectivamente.
* Fornecer um dispositivo que aceite uma ligação segura utilizando certificados.
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
| **Componente**               | Gateway da nuvem iot  | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | Escolha gateway - Azure IoT Hub |
| **Referências**              | [Tokens de segurança do hub Azure IoT](https://azure.microsoft.com/documentation/articles/iot-hub-sas-tokens/) |
| **Passos** | Utilize por credenciais de autenticação por dispositivo utilizando tokens SaS com base na chave do Dispositivo ou no Certificado de Cliente, em vez de políticas de acesso partilhado ao nível do IoT Hub. Isto impede a reutilização de fichas de autenticação de um dispositivo ou porta de entrada de campo por outro |

## <a name="ensure-that-only-the-required-containers-and-blobs-are-given-anonymous-read-access"></a><a id="req-containers-anon"></a>Certifique-se de que apenas os recipientes e bolhas necessários recebem acesso de leitura anónimo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | StorageType - Blob |
| **Referências**              | [Gerir o acesso de leitura anónimo a contentores e bolhas, Assinaturas](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/) [de Acesso Partilhado, Parte 1: Compreender o modelo SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) |
| **Passos** | <p>Por predefinição, um recipiente e quaisquer bolhas no seu interior só podem ser acedidos pelo proprietário da conta de armazenamento. Para dar aos utilizadores anónimos que leiam permissões a um contentor e às suas bolhas, é possível definir as permissões do contentor para permitir o acesso do público. Os utilizadores anónimos podem ler bolhas dentro de um recipiente acessível ao público sem autenticar o pedido.</p><p>Os contentores oferecem as seguintes opções para gerir o acesso dos contentores:</p><ul><li>Acesso completo à leitura pública: Os dados do contentor e da bolha podem ser lidos através de pedido anónimo. Os clientes podem enumerar bolhas dentro do contentor através de pedido anónimo, mas não podem enumerar os contentores dentro da conta de armazenamento.</li><li>O acesso ao público apenas para bolhas: Os dados blob dentro deste contentor podem ser lidos através de pedido anónimo, mas os dados do contentor não estão disponíveis. Os clientes não podem enumerar bolhas dentro do contentor através de pedido anónimo</li><li>Sem acesso público à leitura: Os dados do contentor e do blob só podem ser lidos pelo proprietário da conta</li></ul><p>O acesso anónimo é o melhor para cenários em que certas bolhas devem estar sempre disponíveis para acesso de leitura anónima. Para um controlo mais fino, pode-se criar uma assinatura de acesso partilhado, que permite delegar acesso restrito utilizando diferentes permissões e durante um intervalo de tempo especificado. Certifique-se de que os contentores e bolhas, que podem conter dados sensíveis, não têm acesso anónimo acidentalmente</p>|

## <a name="grant-limited-access-to-objects-in-azure-storage-using-sas-or-sap"></a><a id="limited-access-sas"></a>Conceder acesso limitado a objetos em armazenamento Azure utilizando SAS ou SAP

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias Aplicáveis** | Genérica |
| **Atributos**              | N/D |
| **Referências**              | [Assinaturas de acesso partilhado, Parte 1: Compreender o modelo SAS,](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) [Assinaturas de Acesso Partilhado, Parte 2: Criar e utilizar um SAS com armazenamento Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-2/), [Como delegar acesso a objetos na sua conta utilizando assinaturas](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies) de acesso partilhado e políticas de acesso armazenadas |
| **Passos** | <p>A utilização de uma assinatura de acesso partilhado (SAS) é uma forma poderosa de conceder acesso limitado a objetos numa conta de armazenamento a outros clientes, sem ter de expor a chave de acesso à conta. O SAS é um URI que engloba nos seus parâmetros de consulta toda a informação necessária para o acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com o SAS, o cliente só precisa de passar no SAS para o construtor ou método apropriado.</p><p>Pode utilizar um SAS quando pretender fornecer acesso aos recursos na sua conta de armazenamento a um cliente que não é de confiança com a chave da conta. As chaves da sua conta de armazenamento incluem uma chave primária e secundária, ambas que concedem acesso administrativo à sua conta e todos os recursos da mesmas. Expor qualquer uma das chaves da sua conta abre a sua conta à possibilidade de uso malicioso ou negligente. As assinaturas de acesso partilhado fornecem uma alternativa segura que permite que outros clientes leiam, escrevam e apaguem dados na sua conta de armazenamento de acordo com as permissões que concedeu, e sem necessidade da chave da conta.</p><p>Se tiver um conjunto lógico de parâmetros semelhantes a cada vez, usar uma Política de Acesso Armazenada (SAP) é uma ideia melhor. Uma vez que a utilização de um SAS derivado de uma Política de Acesso Armazenada dá-lhe a capacidade de revogar imediatamente esse SAS, é a melhor prática recomendada para sempre utilizar políticas de acesso armazenadas quando possível.</p>|
