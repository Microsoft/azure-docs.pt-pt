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
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27c0d3055512145396f204fdb17e9375f65a0db3
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562329"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Histórico de Versões
A equipe de Azure Active Directory atualiza regularmente Azure AD Connect Health com novos recursos e funcionalidades. Este artigo lista as versões e os recursos que foram lançados.  

> [!NOTE]
> Os agentes do Connect Health são atualizados automaticamente quando a nova versão é liberada. Verifique se as configurações de atualização automática estão habilitadas em portal do Azure. 
>

O Azure AD Connect Health para sincronização é integrado à instalação do Azure AD Connect. Leia mais sobre [Azure ad Connect histórico de lançamento](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) para obter comentários sobre o recurso, vote no [canal de voz do usuário do Connect Health](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="july-2019"></a>Julho de 2019
**Atualização do agente**
* Agente de Azure AD Connect Health para AD FS (versão 3.1.59.0) 
   1. Alteração de texto em TestWindowsTransport
   2. Alterações para o carregamento AD FS RP
   
* Agente de Azure AD Connect Health para AD FS (versão 3.1.56.0) 
   1. Adicionar teste TestWindowsTransport e remover verificações de pontos de extremidade WsTrust no teste CheckOffice365Endpoints
   2. Informações do sistema operacional de log e do .NET
   3. Aumente o tamanho de carregamento da mensagem de configuração do RP para 1MB.
   4. Correções de erros
   
* Agente de Azure AD Connect Health para AD DS (versão 3.1.56.0) 
   1. Informações do sistema operacional de log e do .NET 
   2. Correções de erros

## <a name="may-2019"></a>Maio de 2019
**Atualização do agente:** 
* Agente de Azure AD Connect Health para AD FS (versão 3.1.51.0) 
   1. Correção de bug para distinguir entre várias entradas que compartilham a mesma ID de solicitação de cliente.
   2. Correção de bug para analisar erros de nome de usuário/senha inválidos em servidores localizados no idioma.   

## <a name="april-2019"></a>Abril de 2019
**Atualização do agente:** 
* Agente de Azure AD Connect Health para AD FS (versão 3.1.46.0) 
   1. Corrigir verificação do processo de alerta de SPN duplicado para ADFS

## <a name="march-2019"></a>Março de 2019
**Atualização do agente:** 
* Agente de Azure AD Connect Health para AD DS (versão 3.1.41.0)  
   1. Coleção de versões do .NET
   2. Melhoria da coleta do contador de desempenho quando faltam determinadas categorias
   3. Correção de bug ao impedir a geração de várias instâncias do agente de monitoramento

* Agente de Azure AD Connect Health para AD FS (versão 3.1.41.0) 
   1. Integrar e atualizar scripts de teste do AD FS usando o ADFSToolBox
   2. Implementar coleção de versões do .NET
   3. Melhoria da coleta do contador de desempenho quando faltam determinadas categorias
   4. Correção de bug ao impedir a geração de várias instâncias do agente de monitoramento


## <a name="november-2018"></a>Novembro de 2018
**Novos recursos de GA:** 
* Azure AD Connect Health para sincronizar-diagnosticar e corrigir erros de sincronização de atributos duplicados do portal

**Atualização do agente:** 
* Agente de Azure AD Connect Health para AD DS (versão 3.1.24.0) 
   1. Protocolo TLS versão 1,2 de conformidade e imposição de camada
   2. Reduzir o ruído de alerta do catálogo global
   3. Correções de bugs de registro do agente de integridade

* Agente de Azure AD Connect Health para AD FS (versão 3.1.24.0)  
   1. Protocolo TLS versão 1,2 de conformidade e imposição de camada
   2. Suporte do Test-ADFSRequestToken para o sistema operacional localizado
   3. Problema de bloqueio EventHandler do agente de diagnóstico resolvido
   4. Correções de bugs de registro do agente de integridade

## <a name="august-2018"></a>Agosto de 2018 
*  Azure AD Connect Health agente para sincronização (versão 3.1.7.0) lançado com Azure AD Connect versão 1.1.880.0    
   1. Hotfix para [alto problema de CPU do agente de monitoramento com versões do .NET Framework KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>junho de 2018 
**Novos recursos de visualização:** 
* Azure AD Connect Health para sincronizar-diagnosticar e corrigir erros de sincronização de atributos duplicados do portal 

**Atualização do agente:** 
* Agente de Azure AD Connect Health para AD DS (versão 3.1.7.0)    
  1. Hotfix para [alto problema de CPU do agente de monitoramento com versões do .NET Framework KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Agente de Azure AD Connect Health para AD FS (versão 3.1.7.0)  
  1. Hotfix para [alto problema de CPU do agente de monitoramento com versões do .NET Framework KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Correções de resultados de teste no servidor ADFS 2016 secundário
   
* Agente de Azure AD Connect Health para AD FS (versão 3.1.2.0)  
  1. Hotfix para gerenciamento de memória do agente e alertas relacionados especificamente para a versão 3.0.244.0


## <a name="may-2018"></a>Maio de 2018
**Atualização do agente:**
* Agente de Azure AD Connect Health para AD DS (versão 3.0.244.0)
  1. Melhoria da privacidade do agente  
  2. Correções de bugs e aprimoramentos gerais

* Agente de Azure AD Connect Health para AD FS (versão 3.0.244.0)
  1. Melhorias do serviço de diagnóstico de agente e do módulo do PowerShell relacionado
  2. Melhoria da privacidade do agente  
  3. Correções de bugs e aprimoramentos gerais

* Azure AD Connect Health agente para sincronização (versão 3.0.164.0) lançado com Azure AD Connect versão 1.1.819.0 
  1. Melhoria da privacidade do agente  
  2. Correções de bugs e aprimoramentos gerais


## <a name="march-2018"></a>Março de 2018
**Novos recursos de visualização:**
* Azure AD Connect Health para relatório e alerta de IP de AD FS arriscados.

**Atualização do agente:**

* Agente de Azure AD Connect Health para AD DS (versão 3.0.176.0)
  1. Melhorias de disponibilidade do agente 
  2. Correções de bugs e aprimoramentos gerais
* Agente de Azure AD Connect Health para AD FS (versão 3.0.176.0)
  1. Melhorias de disponibilidade do agente 
  2. Correções de bugs e aprimoramentos gerais
* Azure AD Connect Health agente para sincronização (versão 3.0.129.0) lançado com Azure AD Connect versão 1.1.750.0  
  1. Melhorias de disponibilidade do agente 
  2. Correções de bugs e aprimoramentos gerais

## <a name="december-2017"></a>Dezembro de 2017
**Atualização do agente:**

* Agente de Azure AD Connect Health para AD DS (versão 3.0.145.0)
  1. Melhorias de disponibilidade do agente 
  2. Novos comandos de solução de problemas do agente adicionados
  3. Correções de bugs e aprimoramentos gerais
* Agente de Azure AD Connect Health para AD FS (versão 3.0.145.0)
  1. Novos comandos de solução de problemas do agente adicionados
  2. Melhorias de disponibilidade do agente 
  3. Correções de bugs e aprimoramentos gerais
  
## <a name="october-2017"></a>Outubro de 2017
**Atualização do agente:**

 * Azure AD Connect Health agente para sincronização (versão 3.0.129.0) lançado com Azure AD Connect versão 1.1.649.0
<br></br> Correção de um problema de compatibilidade de versão entre Azure AD Connect e Azure AD Connect Health Agent para sincronização. Esse problema afeta os clientes que estão executando Azure AD Connect atualização in-loco para a versão 1.1.647.0, mas atualmente tem o agente de integridade versão 3.0.127.0. Após a atualização, o agente de integridade não poderá mais enviar dados de integridade sobre Azure AD Connect serviço de sincronização ao Azure AD Serviço de Integridade. Com essa correção, a versão do agente de integridade 3.0.129.0 é instalada durante Azure AD Connect atualização in-loco. A versão do agente de integridade 3.0.129.0 não tem problema de compatibilidade com Azure AD Connect versão 1.1.649.0.

## <a name="july-2017"></a>Julho de 2017
**Atualização do agente:**

* Agente de Azure AD Connect Health para AD DS (versão 3.0.68.0)
  1. Correções de bugs e aprimoramentos gerais
  2. Suporte de nuvem soberanas
* Agente de Azure AD Connect Health para AD FS (versão 3.0.68.0)
  1. Correções de bugs e aprimoramentos gerais
  2. Suporte de nuvem soberanas
* Azure AD Connect Health agente para sincronização (versão 3.0.68.0) lançado com Azure AD Connect versão 1.1.614.0
  1. Suporte para Microsoft Azure Governamental Cloud e Microsoft Cloud Alemanha

## <a name="april-2017"></a>Abril de 2017      
**Atualização do agente:**

* Agente de Azure AD Connect Health para AD FS (versão 3.0.12.0)
  1. Correções de bugs e aprimoramentos gerais
* Agente de Azure AD Connect Health para AD DS (versão 3.0.12.0)
  1. Aprimoramentos de upload de contadores de desempenho
  2. Correções de bugs e aprimoramentos gerais

## <a name="october-2016"></a>Outubro de 2016
**Atualização do agente:**

* Agente de Azure AD Connect Health para AD FS (versão 2.6.408.0)
* Melhorias na detecção de endereços IP de cliente em solicitações de autenticação
* Correções de bugs relacionadas a alertas
* Agente de Azure AD Connect Health para AD DS (versão 2.6.408.0)
* Correções de bugs relacionadas a alertas.
* Azure AD Connect Health agente para sincronização (versão 2.6.353.0) lançado com Azure AD Connect versão 1.1.281.0
* Forneça os dados necessários para os relatórios de erros de sincronização
* Correções de bugs relacionadas a alertas

**Novos recursos de visualização:**

* Relatórios de erros de sincronização para Azure AD Connect

**Novas funcionalidades:**

* Azure AD Connect Health para AD FS-o campo endereço IP está disponível no relatório sobre os principais usuários do 50 com nome de usuário/senha inválidos.

## <a name="july-2016"></a>Julho de 2016
**Novos recursos de visualização:**

* [Azure ad Connect Health para AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Janeiro de 2016
**Atualização do agente:**

* Agente de Azure AD Connect Health para AD FS (versão 2.6.91.1512)

**Novas funcionalidades:**

* [Ferramenta de conectividade de teste para agentes de Azure AD Connect Health](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Novembro de 2015
**Novas funcionalidades:**

* Suporte para [controle de acesso baseado em função](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Novos recursos de visualização:**

* [Azure ad Connect Health para sincronização](how-to-connect-health-sync.md).

**Problemas corrigidos:**

* Correções de bugs para erros vistos durante os registros do agente.

## <a name="september-2015"></a>Setembro de 2015
**Novas funcionalidades:**

* Relatório de senha de nome de usuário incorreto para AD FS
* Suporte para configurar o proxy HTTP não autenticado
* Suporte para configurar o agente no Server Core
* Melhorias nos alertas para AD FS
* Melhorias no agente Azure AD Connect Health para AD FS para conectividade e carregamento de dados.

**Problemas corrigidos:**

* Correções de bugs em informações de uso para AD FS tipos de erro.

## <a name="june-2015"></a>Junho de 2015
**Versão inicial do Azure AD Connect Health para AD FS e AD FS proxy.**

**Novas funcionalidades:**

* Alertas para monitorar AD FS e AD FS servidores proxy com notificações por email.
* Acesso fácil a AD FS topologia e padrões em AD FS contadores de desempenho.
* Tendência em solicitações de token bem-sucedidas em servidores AD FS agrupados por aplicativos, métodos de autenticação, local de rede de solicitação, etc.
* Tendências na solicitação com falha em servidores AD FS agrupados por aplicativos, tipos de erro etc.
* Implantação de agente mais simples usando credenciais de administrador global do Azure AD.  

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [monitorar sua infraestrutura de identidade local e os serviços de sincronização na nuvem](whatis-hybrid-identity-health.md).

