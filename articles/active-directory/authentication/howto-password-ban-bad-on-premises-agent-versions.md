---
title: Histórico de lançamento de agente de proteção de palavras-passe - Azure Ative Directory
description: Versão de documentos versão e mudança de comportamento histórico
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74847854"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Histórico da versão do agente de proteção de passwords Azure AD

## <a name="121250"></a>1.2.125.0

Data de lançamento: 3/22/2019

* Corrija pequenos erros de tipografia em mensagens de registo de eventos
* Atualizar o acordo da EULA para a versão final da Disponibilidade Geral

> [!NOTE]
> Construir 1.2.125.0 é a construção da Disponibilidade Geral. Obrigado mais uma vez a todos forneceram feedback sobre o produto!

## <a name="121160"></a>1.2.116.0

Data de lançamento: 3/13/2019

* Os Get-AzureADPasswordProtectionProxy e Get-AzureADPasswordProtectionDCAgent cmdlets reportam agora a versão de software e o atual inquilino do Azure com as seguintes limitações:
  * A versão de software e os dados do inquilino Azure só estão disponíveis para agentes de DC e proxies que executam a versão 1.2.116.0 ou posterior.
  * Os dados do inquilino azul não podem ser comunicados até que tenha ocorrido um re-registo (ou renovação) do representante ou da floresta.
* O serviço Proxy agora requer que .NET 4.7 esteja instalado.
  * .NET 4.7 já deve ser instalado num Servidor Windows totalmente atualizado. Se não for esse o caso, descarregue e execute o instalador encontrado no [instalador offline .NET Framework 4.7 para o Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * Nos sistemas Server Core poderá ser necessário passar a bandeira /q para o instalador .NET 4.7 para que tenha sucesso.
* O serviço Proxy agora suporta a atualização automática. A atualização automática utiliza o serviço Microsoft Azure AD Connect Agent Updater, instalado lado a lado com o serviço Proxy. A atualização automática é on por defeito.
* A atualização automática pode ser ativada ou desativada utilizando o Set-AzureADPasswordProtectionProxyConfiguration cmdlet. A definição atual pode ser consultada utilizando o Get-AzureADPasswordProtectionProxyConfiguration cmdlet.
* O binário de serviço para o serviço de agente de DC foi renomeado para AzureADPasswordProtectionDCAgent.exe.
* O binário de serviço para o serviço Proxy foi renomeado para AzureADPasswordProtectionProxy.exe. As regras de firewall podem ter de ser modificadas em conformidade se uma firewall de terceiros estiver em uso.
  * NOTA: se um ficheiro config de procuração em http estiver a ser utilizado numa instalação anterior da Proxy, terá de ser renomeado (de *proxyservice.exe.config* para *AzureADPasswordProtectionProxy.exe.config) *após esta atualização.
* Todas as verificações de funcionalidades limitadas foram removidas do agente DC.
* Pequenas correções de insetos e melhorias no registo.

## <a name="12650"></a>1.2.65.0

Data de lançamento: 2/1/2019

Alterações:

* O agente DC e o serviço de procuração são agora suportados no Server Core. Os requisitos do Mininimum OS são inalterados de antes: Windows Server 2012 para agentes DC e Windows Server 2012 R2 para proxies.
* Os Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest cmdlets suportam agora os modos de autenticação Azure baseados em código de dispositivo.
* O Get-AzureADPasswordProtectionDCAgent cmdlet ignorará os pontos de ligação de serviço mutilados e/ou inválidos. Isto corrige o bug onde os controladores de domínio às vezes aparecem várias vezes na saída.
* O Get-AzureADPasswordProtectionSummaryReport cmdlet ignorará os pontos de ligação de serviço mutilados e/ou inválidos. Isto corrige o bug onde os controladores de domínio às vezes aparecem várias vezes na saída.
* O módulo Proxy powershell está agora registado a partir de %ProgramFiles%\WindowsPowerShell\Modules. A variável ambiente PSModulePath da máquina já não é modificada.
* Foi adicionado um novo Get-AzureADPasswordProtectionProxy cmdlet para ajudar na descoberta de proxies registados numa floresta ou domínio.
* O agente DC utiliza uma nova pasta na partilha sysvol para replicar políticas de palavra-passe e outros ficheiros.

   Localização da pasta antiga:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Localização nova da pasta:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Esta alteração foi feita para evitar avisos falsos positivos de "GPO órfão".)

   > [!NOTE]
   > Nenhuma migração ou partilha de dados será feita entre a pasta antiga e a nova pasta. As versões mais antigas do agente DC continuarão a utilizar a localização antiga até serem atualizadas para esta versão ou mais tarde. Uma vez que todos os agentes DC estejam em execução versão 1.2.65.0 ou posterior, a antiga pasta sysvol pode ser eliminada manualmente.

* O agente DC e o serviço de procuração irão agora detetar e eliminar cópias mutiladas dos respetivos pontos de ligação de serviço.
* Cada agente DC eliminará periodicamente pontos de ligação de serviço mutilados e velhos no seu domínio, tanto para os pontos de ligação do agente DC como para os pontos de ligação de serviço proxy. Tanto os pontos de ligação do agente DC como os pontos de ligação de serviço proxy são considerados estagnados se o seu tempo de batimento cardíaco for superior a sete dias.
* O agente dc vai agora renovar o certificado florestal se necessário.
* O serviço Proxy irá agora renovar o certificado de procuração, se necessário.
* Atualizações para algoritmo de validação de palavras-passe: a lista global de palavras-passe proibidas e a lista de senhas proibidas específicas do cliente (se configuradas) são combinadas antes das validações de palavras-passe. Uma determinada palavra-passe pode agora ser rejeitada (falha ou apenas auditoria) se contiver fichas da lista global e específica do cliente. A documentação do registo do evento foi atualizada para refletir isso; consulte [o Monitor Azure AD Password Protection](howto-password-ban-bad-on-premises-monitor.md).
* Correções de desempenho e robustez
* Melhor exploração madeireira

> [!WARNING]
> Funcionalidade limitada: o serviço de agente DC nesta versão (1.2.65.0) deixará de processar pedidos de validação de passwords a partir de 1 de setembro de 2019.  Os serviços de agente dc em lançamentos anteriores (ver lista abaixo) deixarão de processar a partir de 1 de julho de 2019. O serviço de agente DC em todas as versões registará 10021 eventos para o registo de eventos da Administração nos dois meses que antecedem estes prazos. Todas as restrições de limite de tempo serão eliminadas na próxima versão ga. O serviço de agente Proxy não é limitado em qualquer versão, mas ainda deve ser atualizado para a versão mais recente, de modo a aproveitar todas as correções de bugs subsequentes e outras melhorias.

## <a name="12250"></a>1.2.25.0

Data de lançamento: 11/01/2018

Correções:

* O agente dc e o serviço de procuração não devem mais falhar devido a falhas de confiança de certificado.
* O agente DC e o serviço de procuração têm correções adicionais para máquinas compatíveis com FIPS.
* O serviço proxy funcionará agora corretamente num ambiente de rede TLS 1.2.
* Pequenas correções de desempenho e robustez
* Melhor exploração madeireira

Alterações:

* O nível de SO mínimo exigido para o serviço Proxy é agora o Windows Server 2012 R2. O nível de SO mínimo exigido para o serviço de agente DC permanece no Windows Server 2012.
* O serviço Proxy agora requer .NET versão 4.6.2.
* O algoritmo de validação de palavra-passe utiliza uma tabela de normalização de caracteres expandida. Isto pode resultar na rejeição de senhas que foram aceites em versões anteriores.

## <a name="12100"></a>1.2.10.0

Data de lançamento: 8/17/2018

Correções:

* Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest agora suportam a autenticação de vários fatores
* Register-AzureADPasswordProtectionProxy requer um controlador de domínio WS2012 ou posterior no domínio para evitar erros de encriptação.
* O serviço de agente dc é mais fiável sobre a solicitação de uma nova política de senha da Azure no arranque.
* O serviço de agente DC solicitará uma nova política de senha a partir de Azure a cada hora, se necessário, mas irá agora fazê-lo numa hora de início selecionada aleatoriamente.
* O serviço de agente DC deixará de causar um atraso indefinido no novo anúncio de DC quando instalado num servidor antes da sua promoção como réplica.
* O serviço de agente DC irá agora honrar a definição de configuração "Enable password protection on Windows Server Ative Directory"
* Tanto os agentes de DC como os instaladores de procuração irão agora suportar o upgrade no local ao atualizar para futuras versões.

> [!WARNING]
> A atualização no local da versão 1.1.10.3 não está suportada e resultará num erro de instalação. Para atualizar para a versão 1.2.10 ou posterior, tem primeiro de desinstalar completamente o software de serviço do agente DC e do serviço proxy e, em seguida, instalar a nova versão do zero. É necessário re-registo do serviço Proxy de proteção de senha Azure AD.  Não é necessário re-registar a floresta.

> [!NOTE]
> As atualizações no local do software do agente DC exigirão um reboot.

* O agente DC e o serviço de procuração suportam agora a execução num servidor configurado para utilizar apenas algoritmos compatíveis com FIPS.
* Pequenas correções de desempenho e robustez
* Melhor exploração madeireira

## <a name="11103"></a>1.1.10.3

Data de lançamento: 6/15/2018

Lançamento inicial de pré-visualização pública

## <a name="next-steps"></a>Passos seguintes

[Implementar proteção de senha Azure Ad](howto-password-ban-bad-on-premises-deploy.md)
