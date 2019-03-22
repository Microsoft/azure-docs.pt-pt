---
title: Histórico das Versões do Azure AD Connect Health
description: Este documento descreve as versões para o Azure AD Connect Health e o que está incluído nessas versões.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46e70850ba9e5984e36643f1b9ecc9db29eec149
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294540"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Histórico de Versões
A equipa do Azure Active Directory atualiza regularmente o Azure AD Connect Health com novos recursos e funcionalidades. Este artigo lista as versões e funcionalidades que foram lançadas.  

> [!NOTE]
> Ligar agentes são atualizados automaticamente quando é lançada a nova versão do Estado de funcionamento. Certifique-se de que as definições de atualização automática está ativada no portal do Azure. 
>

O Azure AD Connect Health para sincronização está integrado com a instalação do Azure AD Connect. Leia mais sobre [histórico de lançamento do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) para comentários sobre as funcionalidades, votar em [canal de ligar o estado de funcionamento User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="march-2019"></a>Março de 2019
**Atualização do agente:** 
* Agente do Azure AD Connect Health para AD DS (versão 3.1.41.0) 
* Coleção de versão do .NET.
* Melhoria da recolha do contador de desempenho quando determinadas categorias de tem em falta.
* Correção de erros de impedir a geração de várias instâncias de agente de monitorização.

* Agente do Azure AD Connect Health para AD FS (versão 3.1.41.0) 
* Integrar e atualização de scripts de teste do AD FS com ADFSToolBox.
* Coleção de versão do .NET.
* Melhoria da recolha do contador de desempenho quando determinadas categorias de tem em falta.
* Correção de erros de impedir a geração de várias instâncias de agente de monitorização.


## <a name="november-2018"></a>Novembro de 2018
**Novos recursos de disponibilidade geral:** 
* O Azure AD Connect Health para sincronização - diagnosticar e resolver erros de sincronização de atributo duplicado do portal

**Atualização do agente:** 
* Agente do Azure AD Connect Health para AD DS (versão 3.1.24.0) 
* Conformidade de versão 1.2 do protocolo TLS (Transport Layer Security) e imposição de transporte
* Reduzir o ruído de alerta de Catálogo Global
* Correções de erros de registo de agente de estado de funcionamento

* Agente do Azure AD Connect Health para AD FS (versão 3.1.24.0)
* Conformidade de versão 1.2 do protocolo TLS (Transport Layer Security) e imposição de transporte
* Suporte de ADFSRequestToken de teste para o sistema operacional localizado
* Resolvido o problema de bloqueio de EventHandler do agente de diagnóstico
* Correções de erros de registo de agente de estado de funcionamento

## <a name="august-2018"></a>Agosto de 2018 
*  Agente do Azure AD Connect Health para sincronização (versão 3.1.7.0), lançado com o Azure AD Connect versão 1.1.880.0    
   1. Correção para [libera o problema de CPU elevado do agente com o .NET Framework KB de monitorização](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>junho de 2018 
**Novas funcionalidades de pré-visualização:** 
* O Azure AD Connect Health para sincronização - diagnosticar e resolver erros de sincronização de atributo duplicado do portal 

**Atualização do agente:** 
* Agente do Azure AD Connect Health para AD DS (versão 3.1.7.0)    
  1. Correção para [libera o problema de CPU elevado do agente com o .NET Framework KB de monitorização](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Agente do Azure AD Connect Health para AD FS (versão 3.1.7.0)  
  1. Correção para [libera o problema de CPU elevado do agente com o .NET Framework KB de monitorização](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Correções de resultados de teste no servidor secundário do AD FS Server 2016
   
* Agente do Azure AD Connect Health para AD FS (versão 3.1.2.0)  
  1. Correção para o gerenciamento de memória de agente e alertas relacionados especificamente para a versão 3.0.244.0


## <a name="may-2018"></a>Maio de 2018
**Atualização do agente:**
* Agente do Azure AD Connect Health para AD DS (versão 3.0.244.0)
  1. Melhoria de privacidade do agente  
  2. Correções de bugs e melhoramentos gerais

* Agente do Azure AD Connect Health para AD FS (versão 3.0.244.0)
  1. Serviço de diagnóstico de agente e melhorias de módulo do PowerShell relacionadas
  2. Melhoria de privacidade do agente  
  3. Correções de bugs e melhoramentos gerais

* Agente do Azure AD Connect Health para sincronização (versão 3.0.164.0), lançado com o Azure AD Connect versão 1.1.819.0 
  1. Melhoria de privacidade do agente  
  2. Correções de bugs e melhoramentos gerais


## <a name="march-2018"></a>Março de 2018
**Novas funcionalidades de pré-visualização:**
* O Azure AD Connect Health para AD FS - relatório de IP em risco e o alerta.

**Atualização do agente:**

* Agente do Azure AD Connect Health para AD DS (versão 3.0.176.0)
  1. Aprimoramentos de disponibilidade do agente 
  2. Correções de bugs e melhoramentos gerais
* Agente do Azure AD Connect Health para AD FS (versão 3.0.176.0)
  1. Aprimoramentos de disponibilidade do agente 
  2. Correções de bugs e melhoramentos gerais
* Agente do Azure AD Connect Health para sincronização (versão 3.0.129.0), lançado com o Azure AD Connect versão 1.1.750.0  
  1. Aprimoramentos de disponibilidade do agente 
  2. Correções de bugs e melhoramentos gerais

## <a name="december-2017"></a>Dezembro de 2017
**Atualização do agente:**

* Agente do Azure AD Connect Health para AD DS (versão 3.0.145.0)
  1. Aprimoramentos de disponibilidade do agente 
  2. Foram adicionados comandos de resolução de problemas de agente novo
  3. Correções de bugs e melhoramentos gerais
* Agente do Azure AD Connect Health para AD FS (versão 3.0.145.0)
  1. Foram adicionados comandos de resolução de problemas de agente novo
  2. Aprimoramentos de disponibilidade do agente 
  3. Correções de bugs e melhoramentos gerais
  
## <a name="october-2017"></a>Outubro de 2017
**Atualização do agente:**

 * Agente do Azure AD Connect Health para sincronização (versão 3.0.129.0), lançado com o Azure AD Connect versão 1.1.649.0
<br></br> Foi corrigido um problema de compatibilidade de versão entre o Azure AD Connect e o agente do Azure AD Connect Health para sincronização. Este problema afeta os clientes que estejam a executar o Azure AD Connect no local atualizar para versão 1.1.647.0, mas atualmente tem o agente de estado de funcionamento da versão 3.0.127.0. Após a atualização, o agente de estado de funcionamento não podem mais enviar dados de estado de funcionamento sobre o serviço do Azure AD Connect sincronização ao serviço de estado de funcionamento do Azure AD. Com essa correção, o agente de estado de funcionamento da versão 3.0.129.0 é instalado durante a atualização do Azure AD Connect no local. Agente de estado de funcionamento versão 3.0.129.0 não tem problema de compatibilidade com o Azure AD Connect versão 1.1.649.0.

## <a name="july-2017"></a>Julho de 2017
**Atualização do agente:**

* Agente do Azure AD Connect Health para AD DS (versão 3.0.68.0)
  1. Correções de bugs e melhoramentos gerais
  2. Suporte de Clouds soberanas
* Agente do Azure AD Connect Health para AD FS (versão 3.0.68.0)
  1. Correções de bugs e melhoramentos gerais
  2. Suporte de Clouds soberanas
* Agente do Azure AD Connect Health para sincronização (versão 3.0.68.0), lançado com o Azure AD Connect versão 1.1.614.0
  1. Suporte para a Cloud do Microsoft Azure Government e do Microsoft Cloud Alemanha

## <a name="april-2017"></a>Abril de 2017      
**Atualização do agente:**

* Agente do Azure AD Connect Health para AD FS (versão 3.0.12.0)
  1. Correções de bugs e melhoramentos gerais
* Agente do Azure AD Connect Health para AD DS (versão 3.0.12.0)
  1. Melhorias de carregar de contadores de desempenho
  2. Correções de bugs e melhoramentos gerais

## <a name="october-2016"></a>Outubro de 2016
**Atualização do agente:**

* Agente do Azure AD Connect Health para AD FS (versão 2.6.408.0)
* Melhorias na detecção de endereços IP do cliente em pedidos de autenticação
* Correções de erros relacionados com alertas
* Agente do Azure AD Connect Health para AD DS (versão 2.6.408.0)
* Correções de erros relacionados com alertas.
* Agente do Azure AD Connect Health para sincronização (versão 2.6.353.0), lançado com o Azure AD Connect versão 1.1.281.0
* Fornecer os dados necessários para os relatórios de erros de sincronização
* Correções de erros relacionados com alertas

**Novas funcionalidades de pré-visualização:**

* Relatórios de erros de sincronização para o Azure AD Connect

**Novas funcionalidades:**

* Azure AD Connect Health para AD FS - o campo de endereço IP está disponível no relatório sobre os principais 50 utilizadores com o nome de utilizador/palavra-passe incorreta.

## <a name="july-2016"></a>Julho de 2016
**Novas funcionalidades de pré-visualização:**

* [Azure AD Connect Health para AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Janeiro de 2016
**Atualização do agente:**

* Agente do Azure AD Connect Health para AD FS (versão 2.6.91.1512)

**Novas funcionalidades:**

* [Ferramenta de teste de conectividade para o Azure AD ligar os agentes de estado de funcionamento](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembro de 2015
**Novas funcionalidades:**

* Suporte para [controlo de acesso baseado em funções](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Novas funcionalidades de pré-visualização:**

* [O Azure AD Connect Health para sincronização](how-to-connect-health-sync.md).

**Problemas fixos:**

* Correções de erros para erros vistos durante os registos de agente.

## <a name="september-2015"></a>Setembro de 2015
**Novas funcionalidades:**

* Relatório de palavra-passe de nome de utilizador de errado para o AD FS
* Suporte para configurar o Proxy de HTTP não autenticados
* Suporte para configurar o agente no Server core
* Melhorias aos alertas para o AD FS
* Carregar a melhorias no agente do Azure AD Connect Health para AD FS para conectividade e dados.

**Problemas fixos:**

* Correções de bugs em informações de utilização para tipos de erros do AD FS.

## <a name="june-2015"></a>Junho de 2015
**Versão inicial do Azure AD Connect Health para AD FS e Proxy do AD FS.**

**Novas funcionalidades:**

* Alertas de monitorização de servidores do AD FS e Proxy do AD FS com notificações por e-mail.
* Obter acesso fácil a topologia do AD FS e padrões nos contadores de desempenho do AD FS.
* Tendência em pedidos de token com êxito nos servidores do AD FS, agrupados por aplicativos, métodos de autenticação, pedir etc de localização de rede.
* Tendências de pedido falhado em servidores do AD FS, agrupados por aplicativos, etc de tipos de erro.
* Implementação mais simples do agente com as credenciais de Administrador Global do Azure AD.  

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [monitorizar os serviços de infraestrutura e a sincronização de identidade de no local na cloud](whatis-hybrid-identity-health.md).

