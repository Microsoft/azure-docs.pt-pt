---
title: Histórico de lançamento da versão de agente de proteção de palavra-passe do Azure AD no local - Azure Active Directory
description: Histórico de alterações de comportamento e de lançamento da versão de documentos
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd1a96393f3dd9481705e3de8cc42375de7869e7
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311568"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Histórico de versão do agente de proteção de palavra-passe do AD do Azure

## <a name="121160"></a>1.2.116.0

Data de lançamento: 3/13/2019

* Os Get-AzureADPasswordProtectionProxy Get AzureADPasswordProtectionDCAgent cmdlets e agora a versão do software de relatório e o atual do Azure do inquilino com as seguintes limitações:
  * Versão do software e os dados de inquilino do Azure só estão disponíveis para os agentes do DC e proxies de executar a versão 1.2.116.0 ou posterior.
  * Dados de inquilino do Azure não podem ser relatados até um novo registo (ou renovação) do proxy ou floresta tiver ocorrido.
* O serviço de Proxy requer agora que o .NET 4.7 está instalado.
  * .NET 4.7 já deve estar instalado num servidor do Windows totalmente atualizado. Se não for este o caso, transfira e execute o instalador adrese [o .NET Framework 4.7 instalador offline para Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * Em sistemas de núcleo de servidor poderá ser necessário passar o sinalizador /q para o instalador do .NET 4.7 para fazer com que ela tenha êxito.
* O serviço de Proxy agora suporta a atualização automática. A atualização automática utiliza o serviço Atualizador de agente ligar do Microsoft Azure AD que é instalada lado a lado com o serviço de Proxy. A atualização automática está ativada por predefinição.
* A atualização automática pode ser ativada ou desativado usando o cmdlet Set-AzureADPasswordProtectionProxyConfiguration. A definição atual pode ser consultada com o cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* O serviço de binário para o serviço do agente DC foi renomeado para AzureADPasswordProtectionDCAgent.exe.
* O serviço de binário para o serviço de Proxy foi renomeado para AzureADPasswordProtectionProxy.exe. Regras de firewall poderão ter de ser modificados em conformidade, se uma firewall de terceiros está em utilização.
  * Nota: se um ficheiro de configuração de proxy de http estava sendo usado num Proxy anterior instalar, esta terá de ser mudado (de *proxyservice.exe.config* ao *AzureADPasswordProtectionProxy.exe.config*) após este atualização.
* Todas as verificações de funcionalidade de tempo limitado foram removidas do agente DC.
* Correções de erros secundários e melhoramentos do registo.

## <a name="12650"></a>1.2.65.0

Data de lançamento: 2/1/2019

Alterações:

* Serviço de agente e o proxy de controlador de domínio são agora suportados no Server Core. Requisitos de sistema operacional de Mininimum permanecem inalterados em relação antes: Windows Server 2012 para agentes do DC e o Windows Server 2012 R2 para proxies.
* Os cmdlets AzureADPasswordProtectionProxy Registre-se e registe-se-AzureADPasswordProtectionForest suportam agora os modos de autenticação do Azure com base no dispositivo-código.
* O cmdlet Get-AzureADPasswordProtectionDCAgent ignorará os pontos de ligação de serviço mangled e/ou inválido. Isso resolve o bug em que os controladores de domínio, por vezes, apareceria várias vezes na saída.
* O cmdlet Get-AzureADPasswordProtectionSummaryReport ignorará os pontos de ligação de serviço mangled e/ou inválido. Isso resolve o bug em que os controladores de domínio, por vezes, apareceria várias vezes na saída.
* O módulo do powershell de Proxy está agora registado de % ProgramFiles%\WindowsPowerShell\Modules. Variável de ambiente de PSModulePath a máquina já não está a ser modificado.
* Foi adicionado um novo cmdlet Get-AzureADPasswordProtectionProxy para auxiliar na descoberta de proxies registados numa floresta ou domínio.
* O agente de controlador de domínio utiliza uma nova pasta na partilha de sysvol para replicar as políticas de palavra-passe e outros ficheiros.

   Localização da pasta antigo:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nova localização da pasta:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Esta alteração foi feita para evitar avisos de Falso-positivos "GPO órfãos".)

   > [!NOTE]
   > Nenhuma migração ou a partilha de dados será efetuada entre antigo e a nova pasta. As versões mais antigas do agente de controlador de domínio irão continuar a utilizar a localização antiga até atualizados para esta versão ou posterior. Depois de todos os agentes de controlador de domínio estiverem a executar a versão 1.2.65.0 ou posterior, a pasta sysvol antigo pode ser eliminada manualmente.

* O serviço de agente e o proxy do controlador de domínio agora irá detetar e eliminar mangled cópias dos seus pontos de ligação do respetivo serviço.
* Cada agente de DC periodicamente irá eliminar pontos de ligação de serviço mangled e obsoletos em seu domínio, para ambos os DC agente e o proxy de serviço pontos de conexão. Ambos os DC agente e o proxy de serviço pontos de conexão são considerados obsoletos, caso sua timestamp de heartbeat é com mais de sete dias.
* O agente de controlador de domínio agora irá renovar o certificado de floresta, conforme necessário.
* O serviço de Proxy agora irá renovar o certificado de proxy, conforme necessário.
* Atualizações para o algoritmo de validação da palavra-passe: a lista de palavra-passe banidas global e a lista de palavra-passe de banidas do específicas do cliente (se configurada) são combinados antes de validações de palavra-passe. Agora pode ser rejeitada uma determinada palavra-passe (falhar ou só de auditoria) se este contiver tokens na lista global e específicas do cliente. A documentação do registo de eventos foi atualizada para refletir isso; Veja [proteção de palavras-passe do Monitor do Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Desempenho e robustez correções
* A melhoria do registo

> [!WARNING]
> Funcionalidade de tempo limitado: o serviço do agente DC nesta versão (1.2.65.0) irá parar de processamento de pedidos de validação da palavra-passe a partir de 1º de Setembro de 2019.  Serviços do agente DC em edições anteriores (veja a lista abaixo) irão parar o processamento a partir de 1º de Julho de 2019. O serviço do agente DC em todas as versões registrará 10021 eventos no registo de eventos de administrador em dois meses que levou estes prazos. Todas as restrições de limite de tempo serão removidas na próxima versão de disponibilidade geral. O serviço de agente de Proxy não funciona por tempo limitado em qualquer versão, mas ainda deve ser atualizado para a versão mais recente para tirar o máximo proveito de todas as correções de erros subseqüentes e outras melhorias.

## <a name="12250"></a>1.2.25.0

Data de lançamento: 11/01/2018

Correções:

* Serviço de agente e o proxy de controlador de domínio já não deve falhar devido a falhas de confiança de certificado.
* Serviço de agente e o proxy de controlador de domínio tem correções adicionais para máquinas compatíveis com FIPS.
* Serviço de proxy agora funcionará corretamente num ambiente de rede TLS 1.2 só.
* Desempenho de pequenas e robustez correções
* A melhoria do registo

Alterações:

* O mínimo necessário ao nível do SO para o serviço de Proxy é, agora, Windows Server 2012 R2. O nível de SO mínimo necessário para o serviço do agente DC permanece no Windows Server 2012.
* O serviço de Proxy requer agora que o .NET versão 4.6.2.
* O algoritmo de validação da palavra-passe usa uma tabela de normalização de caráter expandido. Isso pode resultar em palavras-passe a ser rejeitadas foram aceites nas versões anteriores.

## <a name="12100"></a>1.2.10.0

Data de lançamento: 8/17/2018

Correções:

* Registre-se-AzureADPasswordProtectionProxy e registre-se-AzureADPasswordProtectionForest agora suportam a autenticação multifator
* Registre-se-AzureADPasswordProtectionProxy requer um WS2012 ou o posterior controlador de domínio no domínio para evitar erros de encriptação.
* Serviço de agente do controlador de domínio é mais fiável sobre o pedido de uma nova política de palavra-passe do Azure na inicialização.
* Serviço do agente DC irá pedir uma nova política de palavra-passe do Azure a cada hora, se necessário, mas agora farão isso numa hora de início selecionado aleatoriamente.
* Serviço de agente do controlador de domínio já não irá causar um atraso indefinido no novo anúncio do controlador de domínio quando instalado num servidor antes da respetiva promoção como uma réplica.
* Serviço de agente do controlador de domínio agora respeitará a definição de configuração "Ativar a proteção de palavra-passe no Windows Server Active Directory"
* Ambos os instaladores de agente e o proxy do controlador de domínio agora suportará a atualização no local ao atualizar para versões futuras.

> [!WARNING]
> Atualização no local da versão 1.1.10.3 não é suportada e irá resultar num erro de instalação. Como atualizar para versão 1.2.10 ou posterior, tem primeiro completamente desinstalar o software de serviço de proxy de agente e controlador de domínio, e instalar a nova versão a partir do zero. Re-registo da proteção de palavra-passe do Azure AD service de Proxy é necessário.  Não é necessário voltar a registar a floresta.

> [!NOTE]
> Atualizações in-loco do software do agente DC exigirá uma reinicialização.

* Serviço de agente e o proxy de DC suportam agora em execução num servidor configurado para utilizar apenas os algoritmos em conformidade com FIPS.
* Desempenho de pequenas e robustez correções
* A melhoria do registo

## <a name="11103"></a>1.1.10.3

Data de lançamento: 6/15/2018

Versão de pré-visualização pública inicial

## <a name="next-steps"></a>Passos Seguintes

[Implementar a proteção de palavras-passe do AD do Azure](howto-password-ban-bad-on-premises-deploy.md)
