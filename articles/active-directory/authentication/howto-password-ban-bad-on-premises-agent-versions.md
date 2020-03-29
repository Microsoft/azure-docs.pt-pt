---
title: Histórico de lançamento de agente de proteção de passwords - Diretório Ativo Azure
description: Versão de documentos versão versão e mudança de comportamento histórico
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fd33388cb1bdf7c87c44fb3273c6850122a0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847854"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Histórico de versão do agente de proteção de passwords Azure AD

## <a name="121250"></a>1.2.125.0

Data de lançamento: 3/22/2019

* Corrigir pequenos erros de tipografia em mensagens de registo de eventos
* Atualizar o acordo EULA para a versão final da Disponibilidade Geral

> [!NOTE]
> A construção 1.2.125.0 é a construção de Disponibilidade Geral. Obrigado a todos mais uma vez forneceram feedback sobre o produto!

## <a name="121160"></a>1.2.116.0

Data de lançamento: 3/13/2019

* O Get-AzureADPasswordProtectionProxy e get-AzureADPasswordProtectionDCAgent cmdlets reportam agora a versão do software e o atual inquilino Azure com as seguintes limitações:
  * A versão de software e os dados do inquilino Azure só estão disponíveis para agentes de DC e proxies que executam a versão 1.2.116.0 ou posterior.
  * Os dados do arrendatário azure não podem ser comunicados até que tenha ocorrido um reregisto (ou renovação) do proxy ou da floresta.
* O serviço Proxy exige agora que .NET 4.7 esteja instalado.
  * .NET 4.7 já deve ser instalado num Servidor Windows totalmente atualizado. Se não for esse o caso, descarregue e execute o instalador encontrado no [instalador offline .NET Framework 4.7 para windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * Nos sistemas Server Core pode ser necessário passar a bandeira /q para o instalador .NET 4.7 para que tenha sucesso.
* O serviço Proxy suporta agora a atualização automática. A atualização automática utiliza o serviço de atualização do Agente de Ligação AD Microsoft Azure, instalado lado a lado com o serviço Proxy. A atualização automática está a ser adotada por defeito.
* A atualização automática pode ser ativada ou desativada utilizando o cmdlet Set-AzureADPasswordProtectionProxyConfiguration. A definição atual pode ser consultada utilizando o Cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* O binário de serviço para o serviço de agente DC foi renomeado para AzureADPasswordProtectionDCAgent.exe.
* O binário de serviço para o serviço Proxy foi renomeado para AzureADPasswordProtectionProxy.exe. As regras de firewall podem ter de ser modificadas em conformidade se uma firewall de terceiros estiver em uso.
  * NOTA: se um ficheiro http proxy config estivesse a ser utilizado numa instalação anterior da Proxy, este terá de ser renomeado (de *proxyservice.exe.config* para *AzureADPasswordProtectionProxy.exe.config*) após esta atualização.
* Todas as verificações de funcionalidade sem tempo foram removidas do agente DC.
* Pequenas correções de bugs e melhorias de registo.

## <a name="12650"></a>1.2.65.0

Data de lançamento: 2/1/2019

Alterações:

* O agente DC e o serviço proxy são agora suportados no Server Core. Os requisitos do Mininimum OS mantêm-se inalterados face a antes: Windows Server 2012 para agentes DC e Windows Server 2012 R2 para proxies.
* Os cmdlets Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest suportam agora modos de autenticação Azure baseados em código seletiva.
* O Get-AzureADPasswordProtectionDCAgent cmdlet ignorará pontos de ligação de serviço mutilados e/ou inválidos. Isto corrige o bug onde os controladores de domínio aparecem várias vezes na saída.
* O Get-AzureADPasswordProtectionSummaryReport cmdlet ignorará pontos de ligação de serviço mutilados e/ou inválidos. Isto corrige o bug onde os controladores de domínio aparecem várias vezes na saída.
* O módulo Proxy powershell está agora registado a partir de %ProgramFiles%\WindowsPowerShell\Módulos. A variável ambiente PSModulePath da máquina já não é modificada.
* Um novo Get-AzureADPasswordProtectionProxy cmdlet foi adicionado para ajudar na descoberta de proxies registados em uma floresta ou domínio.
* O agente dc usa uma nova pasta na parte sysvol para replicar políticas de password e outros ficheiros.

   Localização antiga da pasta:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nova localização da pasta:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Esta alteração foi feita para evitar avisos falsos positivos de "GPO órfão".)

   > [!NOTE]
   > Não será feita nenhuma migração ou partilha de dados entre a pasta antiga e a nova pasta. As versões mais antigas do agente DC continuarão a utilizar a localização antiga até serem atualizadas para esta versão ou posteriormente. Uma vez que todos os agentes de DC estejam a executar a versão 1.2.65.0 ou mais tarde, a antiga pasta sysvol pode ser eliminada manualmente.

* O agente de DC e o serviço proxy irão agora detetar e eliminar cópias mutiladas dos respetivos pontos de ligação de serviço.
* Cada agente dc eliminará periodicamente pontos de ligação de serviço mutilados e velhos no seu domínio, tanto para os pontos de ligação do agente DC como para os pontos de ligação ao serviço proxy. Tanto os pontos de ligação do agente DC como os pontos de ligação proxy são considerados estancadas se o seu carimbo de batimento cardíaco for superior a sete dias.
* O agente de DC vai agora renovar o certificado florestal conforme necessário.
* O serviço Proxy irá agora renovar o certificado de procuração conforme necessário.
* Atualizações para algoritmo de validação de passwords: a lista global de passwords proibidas e a lista de palavras-passe proibida seleto seleções (se configuradas) são combinadas antes das validações de palavras-passe. Uma determinada palavra-passe pode agora ser rejeitada (falha ou apenas auditoria) se contiver fichas da lista global e específica do cliente. A documentação do registo do evento foi atualizada para refletir isto; por favor, consulte [monitor Azure AD Password Protection](howto-password-ban-bad-on-premises-monitor.md).
* Correções de desempenho e robustez
* Melhor exploração madeireira

> [!WARNING]
> Funcionalidade limitada no tempo: o serviço de agente DC nesta versão (1.2.65.0) deixará de processar pedidos de validação de passwords a partir de 1 de setembro de 2019.  Os serviços de agente da DC em lançamentos anteriores (ver lista abaixo) deixarão de ser processados a partir de 1 de julho de 2019. O serviço de agente dc em todas as versões irá registar eventos de 10021 no registo do evento Admin nos dois meses que antecedem estes prazos. Todas as restrições de tempo serão removidas na próxima versão da AG. O serviço proxy agente não é limitado em qualquer versão, mas ainda deve ser atualizado para a versão mais recente, a fim de tirar partido de todas as correções subsequentes de bugs e outras melhorias.

## <a name="12250"></a>1.2.25.0

Data de lançamento: 11/01/2018

Correções:

* O agente de DC e o serviço proxy não devem mais falhar devido a falhas de fidedignidade de certificados.
* O agente dc e o serviço proxy têm correções adicionais para máquinas compatíveis com FIPS.
* O serviço proxy funcionará agora corretamente num ambiente de rede tLS 1.2.
* Pequenas correções de desempenho e robustez
* Melhor exploração madeireira

Alterações:

* O nível mínimo de OS exigido para o serviço Proxy é agora o Windows Server 2012 R2. O nível mínimo de OS exigido para o serviço de agente DC permanece no Windows Server 2012.
* O serviço Proxy requer agora a versão .NET 4.6.2.
* O algoritmo de validação de palavra-passe usa uma tabela de normalização de caracteres expandido. Isto pode resultar na rejeição de palavras-passe que foram aceites em versões anteriores.

## <a name="12100"></a>1.2.10.0

Data de lançamento: 8/17/2018

Correções:

* Registr-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest suportam agora a autenticação de vários fatores
* O Register-AzureADPasswordProtectionProxy requer um controlador de domínio WS2012 ou posterior no domínio para evitar erros de encriptação.
* O serviço de agente da DC é mais fiável em solicitar uma nova política de senha saqueada pelo Azure no arranque.
* O serviço de agente dc solicitará uma nova política de senha sairá do Azure a cada hora, se necessário, mas agora fá-lo-á numa hora de início selecionada aleatoriamente.
* O serviço de agente DC deixará de causar um atraso indefinido no novo anúncio de DC quando instalado num servidor antes da sua promoção como réplica.
* O serviço de agente DC irá agora honrar a definição de configuração "Ativar a proteção de passwords no Diretório Ativo do Servidor do Windows"
* Tanto o agente DC como os instaladores proxy irão agora suportar a atualização no local ao atualizar para futuras versões.

> [!WARNING]
> A atualização em vigor a partir da versão 1.1.10.3 não é suportada e resultará num erro de instalação. Para atualizar para a versão 1.2.10 ou mais tarde, tem primeiro de desinstalar completamente o agente dc e o software de serviço proxy e, em seguida, instalar a nova versão de raiz. É necessário reregistar o serviço de proteção de passwords Azure AD Proxy.  Não é necessário reregistar a floresta.

> [!NOTE]
> As atualizações em vigor do software do agente DC exigirão um reboot.

* O agente DC e o serviço proxy suportam agora executar num servidor configurado apenas para utilizar apenas algoritmos compatíveis com fips.
* Pequenas correções de desempenho e robustez
* Melhor exploração madeireira

## <a name="11103"></a>1.1.10.3

Data de lançamento: 6/15/2018

Lançamento inicial de pré-visualização pública

## <a name="next-steps"></a>Passos seguintes

[Implementar proteção de senha seletiva Azure](howto-password-ban-bad-on-premises-deploy.md)
