---
title: Histórico das Versões do Azure AD Connect Health
description: Este documento descreve os lançamentos para a Azure AD Connect Health e o que foi incluído nesses lançamentos.
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
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace8d170a5ba48c00775c3b376df8bb70a337d5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76897027"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Histórico do Lançamento de Versões
A equipa de Diretório Ativo Azure atualiza regularmente a Azure AD Connect Health com novas funcionalidades e funcionalidades. Este artigo lista as versões e funcionalidades que foram lançadas.  

> [!NOTE]
> Os agentes de ligação são atualizados automaticamente quando a nova versão é lançada. Certifique-se de que as definições de atualização automática estão ativadas a partir do portal Azure.
>

A Azure AD Connect Health for Sync está integrada com a instalação Azure AD Connect. Leia mais sobre o histórico de lançamento do [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) Para obter feedback de recurso, vote no [canal Connect Health User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="july-2019"></a>Julho de 2019
**Atualização do Agente**
* Agente azure AD Connect Health para AD FS (versão 3.1.59.0) 
   1. Mudança de texto no TestWindowsTransport
   2. Alterações para upload de RP AD FS
   
* Agente azure AD Connect Health para AD FS (versão 3.1.56.0) 
   1. Adicione teste TestWindowsTransport e remova verificações de pontos finais WsTrust no teste CheckOffice365Endpoints
   2. Log OS e .NET informações
   3. Aumente o tamanho do upload da mensagem de configuração RP para 1MB.
   4. Correções de erros
   
* Agente azure AD Connect Health para DS AD (versão 3.1.56.0) 
   1. Log OS e .NET informações 
   2. Correções de erros

## <a name="may-2019"></a>Maio de 2019
**Atualização do agente:** 
* Agente azure AD Connect Health para AD FS (versão 3.1.51.0) 
   1. Correção de bugs para distinguir entre múltiplos signinos que partilham o mesmo cliente-pedido-id.
   2. Correção de erros de bugs para analisar erros de má utilização/palavra-passe em servidores localizados em idioma.   

## <a name="april-2019"></a>Abril de 2019
**Atualização do agente:** 
* Agente azure AD Connect Health para AD FS (versão 3.1.46.0) 
   1. Corrigir verificar duplicar processo de alerta SPN para ADFS

## <a name="march-2019"></a>Março de 2019
**Atualização do agente:** 
* Agente azure AD Connect Health para DS AD (versão 3.1.41.0)  
   1. Coleção de versão .NET
   2. Melhoria da recolha de contadores de desempenho ao perder determinadas categorias
   3. Correção de bugs na prevenção da desova de várias instâncias do Agente de Monitorização

* Agente azure AD Connect Health para AD FS (versão 3.1.41.0) 
   1. Integrar e atualizar scripts de teste AD FS usando a ADFSToolBox
   2. Implementar a coleção de versão .NET
   3. Melhoria da recolha de contadores de desempenho ao perder determinadas categorias
   4. Correção de bugs na prevenção da desova de várias instâncias do Agente de Monitorização


## <a name="november-2018"></a>Novembro de 2018
**Novas funcionalidades de GA:** 
* Azure AD Connect Health for Sync - Diagnosticar e remediar erros de sincronização duplicados do portal

**Atualização do agente:** 
* Agente azure AD Connect Health para DS AD (versão 3.1.24.0) 
   1. Transport Layer Security (TLS) versão de protocolo 1.2 conformidade e execução
   2. Reduzir o ruído de alerta do Catálogo Global
   3. Correções de bugs de registo de agentes de saúde

* Agente azure AD Connect Health para AD FS (versão 3.1.24.0)  
   1. Transport Layer Security (TLS) versão de protocolo 1.2 conformidade e execução
   2. Suporte do Test-ADFSRequestToken para o sistema operativo localizado
   3. Problema de bloqueio do agente de diagnóstico resolvido EventHandler
   4. Correções de bugs de registo de agentes de saúde

## <a name="august-2018"></a>Agosto de 2018 
*  Agente azure AD Connect Health para Sync (versão 3.1.7.0) lançado com a versão 1.1.880.0 do Azure AD Connect    
   1. Hotfix para [alta questão de CPU de monitoring agent com .NET Framework KB releases](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>junho de 2018 
**Novas funcionalidades de pré-visualização:** 
* Azure AD Connect Health for Sync - Diagnosticar e remediar erros de sincronização duplicados do portal 

**Atualização do agente:** 
* Agente azure AD Connect Health para DS AD (versão 3.1.7.0)    
  1. Hotfix para [alta questão de CPU de monitoring agent com .NET Framework KB releases](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Agente azure AD Connect Health para AD FS (versão 3.1.7.0)  
  1. Hotfix para [alta questão de CPU de monitoring agent com .NET Framework KB releases](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Correção de resultados de teste no servidor secundário Do Servidor ADFS 2016
   
* Agente azure AD Connect Health para AD FS (versão 3.1.2.0)  
  1. Hotfix para gestão de memória de agente e alertas relacionados especificamente para a versão 3.0.244.0


## <a name="may-2018"></a>Maio de 2018
**Atualização do agente:**
* Agente azure AD Connect Health para DS AD (versão 3.0.244.0)
  1. Melhoria da privacidade do agente  
  2. Correções de bugs e melhorias gerais

* Agente azure AD Connect Health para AD FS (versão 3.0.244.0)
  1. Serviço de Diagnóstico de Agente e melhorias relacionadas com módulos PowerShell
  2. Melhoria da privacidade do agente  
  3. Correções de bugs e melhorias gerais

* O agente Azure AD Connect Health para Sync (versão 3.0.164.0) lançado com a versão 1.1.819.0 do Azure AD Connect 
  1. Melhoria da privacidade do agente  
  2. Correções de bugs e melhorias gerais


## <a name="march-2018"></a>Março de 2018
**Novas funcionalidades de pré-visualização:**
* Azure AD Connect Health for AD FS - Relatório IP arriscado e alerta.

**Atualização do agente:**

* Agente azure AD Connect Health para DS AD (versão 3.0.176.0)
  1. Melhorias na disponibilidade do agente 
  2. Correções de bugs e melhorias gerais
* Agente azure AD Connect Health para AD FS (versão 3.0.176.0)
  1. Melhorias na disponibilidade do agente 
  2. Correções de bugs e melhorias gerais
* O agente Azure AD Connect Health para Sync (versão 3.0.129.0) lançado com a versão 1.1.750.0 do Azure AD Connect  
  1. Melhorias na disponibilidade do agente 
  2. Correções de bugs e melhorias gerais

## <a name="december-2017"></a>Dezembro de 2017
**Atualização do agente:**

* Agente azure AD Connect Health para DS AD (versão 3.0.145.0)
  1. Melhorias na disponibilidade do agente 
  2. Acrescentou novos comandos de resolução de problemas de agente
  3. Correções de bugs e melhorias gerais
* Agente azure AD Connect Health para AD FS (versão 3.0.145.0)
  1. Acrescentou novos comandos de resolução de problemas de agente
  2. Melhorias na disponibilidade do agente 
  3. Correções de bugs e melhorias gerais
  
## <a name="october-2017"></a>Outubro de 2017
**Atualização do agente:**

 * O agente Azure AD Connect Health para Sync (versão 3.0.129.0) lançado com a versão 1.1.649.0 do Azure AD Connect
<br></br> Corrigiu um problema de compatibilidade entre o Azure AD Connect e o Azure AD Connect Health Agent for Sync. Este problema afeta os clientes que estão a realizar o upgrade do Azure AD Connect no local para a versão 1.1.647.0, mas atualmente tem a versão 3.0.127.0 do Health Agent. Após a atualização, o Agente de Saúde já não pode enviar dados de saúde sobre o Serviço de Sincronização de Ligação Azure AD para o Serviço de Saúde Azure AD. Com esta correção, a versão 3.0.129.0 do Health Agent é instalada durante a atualização do Azure AD Connect no local. A versão 3.0.129.0 do Health Agent não tem problemas de compatibilidade com a versão 1.1.649.0 do Azure AD Connect.

## <a name="july-2017"></a>Julho de 2017
**Atualização do agente:**

* Agente azure AD Connect Health para DS AD (versão 3.0.68.0)
  1. Correções de bugs e melhorias gerais
  2. Apoio soberano à nuvem
* Agente azure AD Connect Health para AD FS (versão 3.0.68.0)
  1. Correções de bugs e melhorias gerais
  2. Apoio soberano à nuvem
* Agente azure AD Connect Health para Sync (versão 3.0.68.0) lançado com a versão 1.1.614.0 do Azure AD Connect
  1. Suporte para Microsoft Azure Government Cloud e Microsoft Cloud Alemanha

## <a name="april-2017"></a>Abril de 2017      
**Atualização do agente:**

* Agente azure AD Connect Health para AD FS (versão 3.0.12.0)
  1. Correções de bugs e melhorias gerais
* Agente azure AD Connect Health para DS AD (versão 3.0.12.0)
  1. Melhorias de upload de contadores de desempenho
  2. Correções de bugs e melhorias gerais

## <a name="october-2016"></a>Outubro de 2016
**Atualização do agente:**

* Agente azure AD Connect Health para AD FS (versão 2.6.408.0)
* Melhorias na deteção de endereços IP do cliente em pedidos de autenticação
* Correções de bugs relacionadas com alertas
* Agente azure AD Connect Health para DS AD (versão 2.6.408.0)
* Correções de bugs relacionadas com Alertas.
* Agente azure AD Connect Health para Sync (versão 2.6.353.0) lançado com a versão 1.1.1.281.0 do Azure AD Connect
* Forneça os dados necessários para os Relatórios de Erros de Sincronização
* Correções de bugs relacionadas com Alertas

**Novas funcionalidades de pré-visualização:**

* Relatórios de erros de sincronização para Azure AD Connect

**Novas funcionalidades:**

* Azure AD Connect Health for AD FS - Campo de endereçoIP está disponível no relatório sobre os 50 utilizadores com mau nome de utilizador/palavra-passe.

## <a name="july-2016"></a>Julho de 2016
**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para DS AD](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Janeiro de 2016
**Atualização do agente:**

* Agente azure AD Connect Health para AD FS (versão 2.6.91.1512)

**Novas funcionalidades:**

* [Ferramenta de conectividade de teste para agentes de saúde Azure AD Connect](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembro de 2015
**Novas funcionalidades:**

* Suporte para [controlo de acesso baseado em funções](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para sincronização](how-to-connect-health-sync.md).

**Questões fixas:**

* Correção de bugs para erros observados durante os registos do agente.

## <a name="september-2015"></a>Setembro de 2015
**Novas funcionalidades:**

* Relatório de palavra-passe de username errado para AD FS
* Suporte para configurar proxy HTTP não autenticado
* Suporte para configurar agente no núcleo do Servidor
* Melhorias nos Alertas para AD FS
* Melhorias no Agente de Saúde Azure AD Connect para AD FS para conectividade e upload de dados.

**Questões fixas:**

* Correções de bugs em Insights de utilização para tipos de erro AD FS.

## <a name="june-2015"></a>Junho de 2015
**Lançamento inicial de Azure AD Connect Health para AD FS e AD FS Proxy.**

**Novas funcionalidades:**

* Alertas para monitorização de servidores AD FS e AD FS Proxy com notificações de e-mail.
* Fácil acesso à topologia e padrões de topologia AD FS em Contadores de Desempenho AD FS.
* Tendência em pedidos de fichas bem-sucedidos em servidores AD FS agrupados por Aplicações, Métodos de Autenticação, Localização da Rede de Pedidos, etc.
* Tendências no pedido falhado em servidores AD FS agrupados por Aplicações, Tipos de Erro, etc.
* Implementação de agente mais simples utilizando credenciais de administração global da AD Azure.  

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a sua infraestrutura de identidade no local e os serviços de sincronização na nuvem.](whatis-hybrid-identity-health.md)

