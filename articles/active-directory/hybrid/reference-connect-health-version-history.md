---
title: Histórico das Versões do Azure AD Connect Health
description: Este documento descreve as versões para Azure AD Connect Health e o que foi incluído nessas versões.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/10/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408e972eb5950baa75fa1bdf33fb2cd507154bcb
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612845"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Histórico do Lançamento de Versões
A equipa do Azure Ative Directory atualiza regularmente o Azure AD Connect Health com novas funcionalidades e funcionalidades. Este artigo lista as versões e funcionalidades que foram lançadas.  

> [!NOTE]
> Os agentes da Connect Health são atualizados automaticamente quando a nova versão é lançada. Certifique-se de que as definições de atualização automática estão ativadas a partir do portal Azure.
>

Azure AD Connect Health for Sync está integrado com a instalação Azure AD Connect. Leia mais sobre [o histórico de lançamentos do Azure AD Connect](./reference-connect-version-history.md) Para obter feedback da funcionalidade, vote no canal Connect Health User [Voice](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="march-2021"></a>março de 2021
**Atualização do Agente**

- Azure AD Connect Health agent for AD FS (versão 3.1.95.0)

  - Corrija para resolver o nome de utilizador formatado NT4 a um UPN durante os eventos de entrada.
  - Corrija para identificar cenários de identificadores de aplicações incorretos com um código de erro dedicado.
  - Alterações para adicionar uma nova propriedade para identificador de clientes OAuth.
  - Fixar para apresentar valores corretos nos campos **Protocolo** e **Tipo de Autenticação** em Azure AD Sign-In Report para determinados cenários de inscrição.
  - Fixar para exibir endereços IP no campo de cadeia IP do Azure AD Sign-In por ordem do pedido.
  - Alterações para introduzir um novo campo para diferenciar se a autenticação secundária foi solicitada durante uma entrada.
  - Correção para a propriedade do identificador de aplicação AD FS para exibir no Relatório de Sign-In Azure.

## <a name="april-2020"></a>Abril de 2020
**Atualização do Agente**

- Azure AD Connect Health agent for AD FS (versão 3.1.77.0)

   - Correção de erro para alerta "Invalid Service Principal Name (SPN) for AD FS service", para o qual o alerta estava a reportar incorretamente.


## <a name="july-2019"></a>Julho de 2019
**Atualização do Agente**
* Azure AD Connect Health agent for AD FS (versão 3.1.59.0) 
   1. Alteração de texto no TestWindowsTransport
   2. Alterações para upload de RP AD FS
   
* Azure AD Connect Health agent for AD FS (versão 3.1.56.0) 
   1. Adicione testWindowsTransport e remova os pontos finais da WsTrust no teste CheckOffice365Endpoints
   2. Informações de Login OS e .NET
   3. Aumente o tamanho do upload da mensagem de configuração RP para 1MB.
   4. Correções de erros
   
* Azure AD Connect Health agent for AD DS (versão 3.1.56.0) 
   1. Informações de Login OS e .NET 
   2. Correções de erros

## <a name="may-2019"></a>Maio de 2019
**Atualização do Agente:** 
* Azure AD Connect Health agent for AD FS (versão 3.1.51.0) 
   1. Correção de erro para distinguir entre múltiplos sinais que partilham o mesmo id de pedido de cliente.
   2. Correção de erros para analisar erros de nome de utilizador/palavra-passe em servidores localizados em idioma.   

## <a name="april-2019"></a>Abril de 2019
**Atualização do Agente:** 
* Azure AD Connect Health agent for AD FS (versão 3.1.46.0) 
   1. Corrigir Verificar duplicado processo de alerta SPN para ADFS

## <a name="march-2019"></a>Março de 2019
**Atualização do Agente:** 
* Azure AD Connect Health agent for AD DS (versão 3.1.41.0)  
   1. Coleção de versão .NET
   2. Melhoria da recolha de contadores de desempenho quando falta certas categorias
   3. Correção de insetos na prevenção da desova de múltiplas instâncias do Monitoring Agent

* Azure AD Connect Health agent for AD FS (versão 3.1.41.0) 
   1. Integrar e atualizar scripts de teste AD FS usando ADFSToolBox
   2. Implementar coleção de versão .NET
   3. Melhoria da recolha de contadores de desempenho quando falta certas categorias
   4. Correção de insetos na prevenção da desova de múltiplas instâncias do Monitoring Agent


## <a name="november-2018"></a>Novembro de 2018
**Novas funcionalidades de GA:** 
* Azure AD Connect Health for Sync - Diagnosticar e remediar erros de sincronização de atributos duplicados do portal

**Atualização do Agente:** 
* Azure AD Connect Health agent for AD DS (versão 3.1.24.0) 
   1. Protocolo de Segurança da Camada de Transporte (TLS) versão 1.2 conformidade e execução
   2. Reduzir o ruído de alerta do Catálogo Global
   3. Correções de erros de registo de agentes de saúde

* Azure AD Connect Health agent for AD FS (versão 3.1.24.0)  
   1. Protocolo de Segurança da Camada de Transporte (TLS) versão 1.2 conformidade e execução
   2. Apoio a Test-ADFSRequestToken para sistema operativo localizado
   3. Problema de bloqueio de agente de diagnóstico resolvido EventHandler
   4. Correções de erros de registo de agentes de saúde

## <a name="august-2018"></a>Agosto de 2018 
*  Azure AD Connect Health agent for Sync (versão 3.1.7.0) lançado com a versão AZURE AD Connect versão 1.1.880.0    
   1. Hotfix para [alta emissão de CPU de agente de monitorização com lançamentos KB.Net Framework](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>junho de 2018 
**Novas funcionalidades de pré-visualização:** 
* Azure AD Connect Health for Sync - Diagnosticar e remediar erros de sincronização de atributos duplicados do portal 

**Atualização do Agente:** 
* Azure AD Connect Health agent for AD DS (versão 3.1.7.0)    
  1. Hotfix para [alta emissão de CPU de agente de monitorização com lançamentos KB.Net Framework](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Azure AD Connect Health agent for AD FS (versão 3.1.7.0)  
  1. Hotfix para [alta emissão de CPU de agente de monitorização com lançamentos KB.Net Framework](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Correções de resultados de teste no servidor secundário do ADFS Server 2016
   
* Azure AD Connect Health agent for AD FS (versão 3.1.2.0)  
  1. Hotfix para gestão de memória de agente e alertas relacionados especificamente para a versão 3.0.244.0


## <a name="may-2018"></a>Maio de 2018
**Atualização do Agente:**
* Azure AD Connect Health agent for AD DS (versão 3.0.244.0)
  1. Melhoria da privacidade do agente  
  2. Correções de insetos e melhorias gerais

* Azure AD Connect Health agent for AD FS (versão 3.0.244.0)
  1. Serviço de Diagnóstico de Agente e melhorias relacionadas do módulo PowerShell
  2. Melhoria da privacidade do agente  
  3. Correções de insetos e melhorias gerais

* Azure AD Connect Health agent for Sync (versão 3.0.164.0) lançado com a versão 1.1.819.0 do Azure AD Connect 
  1. Melhoria da privacidade do agente  
  2. Correções de insetos e melhorias gerais


## <a name="march-2018"></a>Março de 2018
**Novas funcionalidades de pré-visualização:**
* Azure AD Connect Health for AD FS - Relatório IP arriscado e alerta.

**Atualização do Agente:**

* Azure AD Connect Health agent for AD DS (versão 3.0.176.0)
  1. Melhorias na disponibilidade do agente 
  2. Correções de insetos e melhorias gerais
* Azure AD Connect Health agent for AD FS (versão 3.0.176.0)
  1. Melhorias na disponibilidade do agente 
  2. Correções de insetos e melhorias gerais
* Azure AD Connect Health agent for Sync (versão 3.0.129.0) lançado com a versão 1.1.750.0 do Azure AD Connect  
  1. Melhorias na disponibilidade do agente 
  2. Correções de insetos e melhorias gerais

## <a name="december-2017"></a>Dezembro de 2017
**Atualização do Agente:**

* Azure AD Connect Health agent for AD DS (versão 3.0.145.0)
  1. Melhorias na disponibilidade do agente 
  2. Adicionou novos comandos de resolução de problemas de agente
  3. Correções de insetos e melhorias gerais
* Azure AD Connect Health agent for AD FS (versão 3.0.145.0)
  1. Adicionou novos comandos de resolução de problemas de agente
  2. Melhorias na disponibilidade do agente 
  3. Correções de insetos e melhorias gerais
  
## <a name="october-2017"></a>Outubro de 2017
**Atualização do Agente:**

 * Azure AD Connect Health agent for Sync (versão 3.0.129.0) lançado com a versão 1.1.649.0 do Azure AD Connect
<br></br> Corrigiu um problema de compatibilidade de versão entre O Azure AD Connect e Azure AD Connect Health Agent for Sync. Este problema afeta os clientes que estão a realizar a atualização do Azure AD Connect no local para a versão 1.1.647.0, mas que atualmente tem a versão 3.0.127.0 do Health Agent. Após a atualização, o Agente de Saúde já não pode enviar dados de saúde sobre o Serviço de Sincronização AZURE AD Connect Para o Serviço de Saúde Azure AD. Com esta correção, a versão 3.0.129.0 do Health Agent é instalada durante a atualização do Azure AD Connect no local. A versão 3.0.129.0 do Health Agent não tem problema de compatibilidade com a versão 1.1.649.0 do Health Agent.

## <a name="july-2017"></a>Julho de 2017
**Atualização do Agente:**

* Azure AD Connect Health agent for AD DS (versão 3.0.68.0)
  1. Correções de insetos e melhorias gerais
  2. Apoio à nuvem soberana
* Azure AD Connect Health agent for AD FS (versão 3.0.68.0)
  1. Correções de insetos e melhorias gerais
  2. Apoio à nuvem soberana
* Azure AD Connect Health agent for Sync (versão 3.0.68.0) lançado com a versão Azure AD Connect 1.1.614.0
  1. Suporte para Microsoft Azure Government Cloud e Microsoft Cloud Germany

## <a name="april-2017"></a>Abril de 2017      
**Atualização do Agente:**

* Azure AD Connect Health agent for AD FS (versão 3.0.12.0)
  1. Correções de insetos e melhorias gerais
* Azure AD Connect Health agent for AD DS (versão 3.0.12.0)
  1. Melhorias no upload dos contadores de desempenho
  2. Correções de insetos e melhorias gerais

## <a name="october-2016"></a>Outubro de 2016
**Atualização do Agente:**

* Azure AD Connect Health agent for AD FS (versão 2.6.408.0)
* Melhorias na deteção de endereços IP do cliente em pedidos de autenticação
* Correções de Bugs relacionadas com alertas
* Azure AD Connect Health agent for AD DS (versão 2.6.408.0)
* Correções de bugs relacionadas com Alertas.
* Azure AD Connect Health agent for Sync (versão 2.6.353.0) lançado com a versão 1.1.281.0 do Azure AD Connect
* Fornecer os dados necessários para os Relatórios de Erro de Sincronização
* Correções de bugs relacionadas com alertas

**Novas funcionalidades de pré-visualização:**

* Relatórios de erro de sincronização para Azure AD Connect

**Novas funcionalidades:**

* O campo de endereços Azure AD Connect Health for AD FS - IP está disponível no relatório sobre os 50 principais utilizadores com mau nome de utilizador/senha.

## <a name="july-2016"></a>Julho de 2016
**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para DS AD](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Janeiro de 2016
**Atualização do Agente:**

* Azure AD Connect Health agent for AD FS (versão 2.6.91.1512)

**Novas funcionalidades:**

* [Ferramenta de conectividade de teste para agentes de saúde de ligação Azure AD](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembro de 2015
**Novas funcionalidades:**

* Suporte ao [controlo de acesso baseado em funções Azure (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac)

**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para sincronização](how-to-connect-health-sync.md).

**Questões fixas:**

* Correções de erros vistos durante registos de agentes.

## <a name="september-2015"></a>Setembro de 2015
**Novas funcionalidades:**

* Relatório de senha de nome de utilizador errado para AD FS
* Suporte para configurar unauthenticed HTTP Proxy
* Suporte para configurar o agente no núcleo do servidor
* Melhorias aos Alertas para FS AD
* Melhorias no Azure AD Connect Health Agent para AD FS para conectividade e upload de dados.

**Questões fixas:**

* Correções de erro em Insights de Utilização para tipos de erros AD FS.

## <a name="june-2015"></a>Junho de 2015
**Lançamento inicial do Azure AD Connect Health para AD FS e AD FS Proxy.**

**Novas funcionalidades:**

* Alertas para monitorização de servidores AD FS e AD FS Proxy com notificações de e-mail.
* Fácil acesso à topologia e padrões de topologia AD FS em Contadores de Desempenho AD FS.
* Tendência em pedidos de token bem sucedidos em servidores AD FS agrupados por Aplicações, Métodos de Autenticação, Localização da Rede de Pedidos, etc.
* Tendências no pedido falhado nos servidores AD FS agrupados por Aplicações, Tipos de Erro, etc.
* Implementação de agente mais simples usando credenciais de administração global da Azure AD.  

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a sua infraestrutura de identidade no local e serviços de sincronização na nuvem.](./whatis-azure-ad-connect.md)
