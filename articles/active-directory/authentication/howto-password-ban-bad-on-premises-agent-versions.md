---
title: Histórico de liberação do agente de proteção de senha-Azure Active Directory
description: Histórico de alterações de versão e comportamento de documentos
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6458f31b21d09e54afb080ffc73598903a9831e0
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381738"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Histórico de versão do agente de proteção de senha do Azure AD

## <a name="121250"></a>1.2.125.0

Data de lançamento: 3/22/2019

* Corrigir erros de digitação secundária nas mensagens do log de eventos
* Atualizar o contrato de EULA para a versão de disponibilidade geral final

> [!NOTE]
> Build 1.2.125.0 é a compilação de disponibilidade geral. Agradecemos novamente a todos os comentários sobre o produto!

## <a name="121160"></a>1.2.116.0

Data de lançamento: 3/13/2019

* Os cmdlets Get-AzureADPasswordProtectionProxy e Get-AzureADPasswordProtectionDCAgent agora relatam a versão do software e o locatário do Azure atual com as seguintes limitações:
  * A versão do software e os dados de locatário do Azure estão disponíveis somente para agentes de DC e proxies que executam a versão 1.2.116.0 ou posterior.
  * Os dados de locatário do Azure podem não ser relatados até que um novo registro (ou renovação) do proxy ou da floresta tenha ocorrido.
* O serviço de proxy agora requer que o .NET 4,7 esteja instalado.
  * O .NET 4,7 já deve estar instalado em um Windows Server totalmente atualizado. Se esse não for o caso, baixe e execute o instalador encontrado no [instalador offline do .NET Framework 4,7 para Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * Em sistemas Server Core, pode ser necessário passar o sinalizador/q para o instalador do .NET 4,7 para que ele tenha êxito.
* O serviço de proxy agora dá suporte à atualização automática. A atualização automática usa o serviço de atualizador do agente Microsoft Azure AD Connect que é instalado lado a lado com o serviço de proxy. A atualização automática está ativada por padrão.
* A atualização automática pode ser habilitada ou desabilitada usando o cmdlet Set-AzureADPasswordProtectionProxyConfiguration. A configuração atual pode ser consultada usando o cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* O binário de serviço para o serviço de agente de DC foi renomeado para AzureADPasswordProtectionDCAgent. exe.
* O binário do serviço para o serviço de proxy foi renomeado para AzureADPasswordProtectionProxy. exe. As regras de firewall podem precisar ser modificadas de acordo se um firewall de terceiros estiver em uso.
  * Observação: se um arquivo de configuração de proxy http estava sendo usado em uma instalação de proxy anterior, será necessário renomeá-lo (de *proxyservice. exe. config* para *AzureADPasswordProtectionProxy. exe. config*) após essa atualização.
* Todas as verificações de funcionalidade limitadas por tempo foram removidas do agente de DC.
* Correções de bugs secundários e melhorias de log.

## <a name="12650"></a>1.2.65.0

Data de lançamento: 2/1/2019

For

* O agente de DC e o serviço de proxy agora têm suporte no Server Core. Os requisitos do sistema operacional Mininimum são inalterados de antes: Windows Server 2012 para agentes de DC e Windows Server 2012 R2 para proxies.
* Os cmdlets Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest agora oferecem suporte aos modos de autenticação do Azure baseados em código de dispositivo.
* O cmdlet Get-AzureADPasswordProtectionDCAgent ignorará os pontos de conexão de serviço desconfigurados e/ou inválidos. Isso corrige o bug em que os controladores de domínio às vezes aparecem várias vezes na saída.
* O cmdlet Get-AzureADPasswordProtectionSummaryReport ignorará os pontos de conexão de serviço desconfigurados e/ou inválidos. Isso corrige o bug em que os controladores de domínio às vezes aparecem várias vezes na saída.
* O módulo proxy PowerShell agora está registrado em%ProgramFiles%\WindowsPowerShell\Modules. A variável de ambiente PSModulePath do computador não é mais modificada.
* Um novo cmdlet Get-AzureADPasswordProtectionProxy foi adicionado para auxiliar na descoberta de proxies registrados em uma floresta ou domínio.
* O agente de DC usa uma nova pasta no compartilhamento de SYSVOL para replicar políticas de senha e outros arquivos.

   Local da pasta antiga:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Novo local da pasta:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Essa alteração foi feita para evitar avisos falsos positivos de "GPO órfão".)

   > [!NOTE]
   > Nenhuma migração ou compartilhamento de dados será feito entre a pasta antiga e a nova pasta. Versões mais antigas do agente de controlador de domínio continuarão a usar o local antigo até serem atualizados para esta versão ou posterior. Depois que todos os agentes de DC estiverem executando a versão 1.2.65.0 ou posterior, a pasta SYSVOL antiga poderá ser excluída manualmente.

* O agente DC e o serviço proxy agora detectarão e excluirão cópias descontadas de seus respectivos pontos de conexão de serviço.
* Cada agente de DC excluirá periodicamente os pontos de conexão de serviço desconfigurados e obsoletos em seu domínio, para os pontos de conexão do agente DC e do serviço proxy. Os pontos de conexão do agente DC e do serviço proxy serão considerados obsoletos se o carimbo de data/hora da pulsação for mais antigo que sete dias.
* O agente de DC agora renovará o certificado de floresta conforme necessário.
* O serviço de proxy agora renovará o certificado de proxy conforme necessário.
* Atualizações para o algoritmo de validação de senha: a lista de senhas globais banidas e a lista de senhas banidas específica do cliente (se configuradas) são combinadas antes de validações de senha. Uma determinada senha pode agora ser rejeitada (falha ou somente auditoria) se ela contiver tokens da lista global e específica do cliente. A documentação do log de eventos foi atualizada para refletir isso; consulte [monitorar a proteção de senha do Azure ad](howto-password-ban-bad-on-premises-monitor.md).
* Correções de desempenho e robustez
* Log aprimorado

> [!WARNING]
> Funcionalidade limitada por tempo: o serviço de agente de controlador de domínio nesta versão (1.2.65.0) irá parar de processar solicitações de validação de senha a partir de 1º de setembro de 2019.  Os serviços de agente de DC em versões anteriores (consulte a lista abaixo) interromperão o processamento a partir de 1º de julho de 2019. O serviço de agente de controlador de domínio em todas as versões registrará 10021 eventos no log de eventos de administrador nos dois meses, levando esses prazos. Todas as restrições de limite de tempo serão removidas na próxima versão de GA. O serviço de agente de proxy não é limitado a tempo em nenhuma versão, mas ainda deve ser atualizado para a versão mais recente a fim de aproveitar todas as correções de bugs subsequentes e outras melhorias.

## <a name="12250"></a>1.2.25.0

Data de lançamento: 11/01/2018

Contida

* O agente DC e o serviço proxy não devem mais falhar devido a falhas de confiança do certificado.
* O agente de DC e o serviço de proxy têm correções adicionais para computadores compatíveis com FIPS.
* O serviço de proxy agora funcionará corretamente em um ambiente de rede somente TLS 1,2.
* Correções de desempenho e robustez secundárias
* Log aprimorado

For

* O nível mínimo necessário do sistema operacional para o serviço de proxy é agora Windows Server 2012 R2. O nível mínimo necessário de sistema operacional para o serviço de agente de controlador de domínio permanece no Windows Server 2012.
* O serviço de proxy agora requer o .NET versão 4.6.2.
* O algoritmo de validação de senha usa uma tabela de normalização de caracteres expandida. Isso pode resultar em senhas rejeitadas que foram aceitas em versões anteriores.

## <a name="12100"></a>1.2.10.0

Data de lançamento: 8/17/2018

Contida

* Register-AzureADPasswordProtectionProxy e Register-AzureADPasswordProtectionForest agora oferecem suporte à autenticação multifator
* O Register-AzureADPasswordProtectionProxy requer um controlador de domínio WS2012 ou posterior no domínio para evitar erros de criptografia.
* O serviço de agente de DC é mais confiável para solicitar uma nova política de senha do Azure na inicialização.
* O serviço de agente DC solicitará uma nova política de senha do Azure a cada hora, se necessário, mas agora fará isso em uma hora de início aleatoriamente selecionada.
* O serviço de agente de controlador de domínio não causará um atraso indefinido no novo anúncio de DC quando instalado em um servidor antes de sua promoção como uma réplica.
* O serviço de agente de DC agora honrará a definição de configuração "Habilitar proteção por senha no Windows Server Active Directory"
* O agente DC e instaladores de proxy agora oferecerão suporte à atualização in-loco ao atualizar para versões futuras.

> [!WARNING]
> Não há suporte para a atualização in-loco da versão 1.1.10.3 e resultará em um erro de instalação. Para atualizar para a versão 1.2.10 ou posterior, primeiro você deve desinstalar completamente o agente de DC e o software de serviço de proxy e, em seguida, instalar a nova versão do zero. É necessário o novo registro do serviço de proxy de proteção de senha do Azure AD.  Não é necessário registrar novamente a floresta.

> [!NOTE]
> As atualizações in-loco do software do agente de DC exigirão uma reinicialização.

* O agente de DC e o serviço de proxy agora dão suporte à execução em um servidor configurado para usar apenas algoritmos compatíveis com FIPS.
* Correções de desempenho e robustez secundárias
* Log aprimorado

## <a name="11103"></a>1.1.10.3

Data de lançamento: 6/15/2018

Versão de visualização pública inicial

## <a name="next-steps"></a>Passos seguintes

[Implantar a proteção de senha do Azure AD](howto-password-ban-bad-on-premises-deploy.md)
