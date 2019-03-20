---
title: Telemetria de pilha do Azure | Documentos da Microsoft
description: Descreve como configurar as definições de telemetria do Azure Stack com o PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 25a832be432f1d1a6d9b18dfc8f7384c564c3060
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103439"
---
# <a name="azure-stack-telemetry"></a>Telemetria de pilha do Azure

Dados de sistema do Azure Stack ou telemetria, é automaticamente carregada para a Microsoft via a experiência do usuário conectado. Dados recolhidos a partir da telemetria do Azure Stack são utilizados pelas equipas de Microsoft principalmente para melhorar as nossas experiências de cliente e para análise de segurança, estado de funcionamento, qualidade e desempenho.

Como um operador do Azure Stack, telemetria pode fornecer informações valiosas para implementações em empresas e dá-lhe uma voz que ajuda a forma as futuras versões do Azure Stack.

> [!NOTE]
> O Azure Stack também pode ser configurado para informações de utilização direta para o Azure para faturação. Isto é necessário para clientes com vários nós do Azure Stack que optam por faturação de pagamento-como-utiliza. Relatórios de utilização é controlada de forma independente de telemetria e não é necessária para os clientes de vários nós que escolher o modelo de capacidade ou para usuários do Kit de desenvolvimento do Azure Stack. Nestes cenários, relatórios de utilização podem ser desativado [utilizando o script de Registro](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Telemetria de pilha do Azure baseia-se o componente telemetria e experiência do usuário conectado Windows Server 2016, que utiliza a [rastreio de eventos para Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) tecnologia de registo para recolher e armazenar dados e eventos de telemetria de rastreio. Componentes de pilha do Azure utilizam a mesma tecnologia de registo para publicar eventos e dados que são recolhidos com o log de eventos do sistema de operativo pública e APIs de rastreio. Exemplos de componentes do Azure Stack incluem o fornecedor de recursos de rede, fornecedor de recursos de armazenamento, fornecedor de recursos de monitoramento e o fornecedor de recursos de atualização. O componente ligado a experiência de utilizador e telemetria encripta os dados com SSL e utiliza a afixação de certificado de mensagens em fila para transmitir dados de telemetria através de HTTPS para o serviço de gestão de dados da Microsoft.

> [!NOTE]
> Para suportar o fluxo de dados de telemetria, porta 443 (HTTPS) tem de estar aberta na sua rede. O componente ligado a experiência de utilizador e telemetria liga ao serviço de gestão de dados da Microsoft em https://v10.vortex-win.data.microsoft.com. O componente ligado a experiência de utilizador e telemetria liga-se também ao https://settings-win.data.microsoft.com para transferir informações de configuração.

## <a name="privacy-considerations"></a>Considerações de privacidade
O serviço ETW encaminha dados de telemetria de volta para o armazenamento de nuvem protegida. O princípio de acesso menos privilegiado de guias para dados de telemetria. Apenas as equipas da Microsoft com uma necessidade de negócio válida têm permissão de acesso para os dados de telemetria. Microsoft não partilha dados pessoais dos nossos clientes com terceiros, exceto a critério do cliente ou para os fins limitados descritos na [declaração de privacidade do Azure Stack](https://privacy.microsoft.com/PrivacyStatement). Compartilhamos relatórios de negócios com OEMs e parceiros que incluem informações de telemetria agregada e anónimos. Decisões de partilha de dados são feitos por uma equipe interna do Microsoft, incluindo partes interessadas de gestão de dados, legais e privacidade.

A Microsoft acredita no e práticas minimização de informações. Esforçamo-nos reunir apenas as informações que precisamos, e podemos armazená-los por apenas tempo que tem de ser necessário fornecer um serviço ou para análise. Muitas das informações sobre como o sistema do Azure Stack e serviços do Azure estão a funcionar é eliminada dentro de seis meses. Resumidos ou dados agregados são mantidos durante um período mais longo.

Estamos cientes de que a privacidade e segurança de informações dos clientes é importante. Trouxemo uma abordagem abrangente e uma execução ponderados a privacidade dos clientes e a proteção de dados do cliente com o Azure Stack. Os administradores de TI tem controlos para personalizar funcionalidades e definições de privacidade em qualquer altura. Nosso compromisso para com transparência e a confiança é claro:
- Estamos abertos com os clientes sobre os tipos de dados, que podemos reunir.
- Colocamos os clientes empresariais no controle — eles podem personalizar as suas próprias definições de privacidade.
- Colocamos segurança e privacidade do cliente pela primeira vez.
- Estamos transparentes sobre como telemetria, é utilizada.
- Telemetria são utilizadas para melhorar as experiências de cliente.

Microsoft não pretenda reunir informações confidenciais, como números de cartão de crédito, nomes de utilizador e palavras-passe, endereços de e-mail ou outras informações confidenciais da mesma forma. Se determinarmos que informações confidenciais foi recebidas inadvertidamente, podemos eliminá-lo.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exemplos de como a Microsoft usa os dados de telemetria
Telemetria desempenha um papel importante em ajudar-na identificar rapidamente e corrigir problemas de confiabilidade essencial em implementações e configurações dos nossos clientes. Informações sobre os dados de telemetria que vamos recolher ajudam-na identificar rapidamente problemas com os serviços ou configurações de hardware. Capacidade da Microsoft para obter esses dados de clientes e melhoramentos no ecossistema do ajuda a aumentar a barra de qualidade de nossas soluções integradas do Azure Stack.

Telemetria também ajuda a Microsoft para compreender melhor como os clientes implementar componentes, utilizar as funcionalidades e utilizar os serviços para alcançar seus objetivos comerciais. Obtenção de informações de dados ajuda a atribuir prioridades a investimentos de engenharia nas áreas que podem afetar diretamente experiências de nossos clientes e cargas de trabalho.

Alguns exemplos incluem a utilização do cliente de contentores, armazenamento e as configurações de rede que estão associadas a funções do Azure Stack. Também utilizaremos as informações para implementar melhoramentos e inteligência em algumas das nossas gestão e monitorização de soluções. Isso ajuda os clientes a diagnosticar problemas de qualidade e poupar dinheiro, tornando menos suporte chama para a Microsoft.

## <a name="manage-telemetry-collection"></a>Gerir coleção de telemetria
Não é recomendável que desativa a telemetria na sua organização como telemetria fornece dados que impulsionam a funcionalidade aprimorada de produto e estabilidade. Reconhecemos no entanto, que em alguns cenários Isto poderá ser necessário.

Nesses casos, pode configurar o nível de telemetria enviado à Microsoft ao utilizar a pré-implementação das definições de registo ou usando a implantação de postagem de pontos finais de telemetria.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Definir o nível de telemetria no registo do Windows
O Editor de registo do Windows é utilizado para definir manualmente o nível de telemetria no computador anfitrião físico antes de implementar o Azure Stack. Se uma política de gestão já existir, como a diretiva de grupo, ele substitui esta definição de registo.

Antes de implementar o Azure Stack do anfitrião do kit de desenvolvimento, inicializar o CloudBuilder.vhdx e execute o seguinte script numa janela do PowerShell elevada:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Os níveis de telemetria são cumulativas e categorizadas em quatro níveis (0 a 3):

**0 (segurança)**. Apenas dados de segurança. Proteger informações necessárias para ajudar a manter o sistema operativo, incluindo dados sobre as definições de componente de telemetria e experiência do usuário conectado e o Windows Defender. Sem telemetria específica do Azure Stack é emitida neste nível.

**1 (básico)**. Os dados de segurança e dados de estado de funcionamento básico e qualidade. Informações básicas do dispositivo, incluindo: dados relacionados com a qualidade, compatibilidade de aplicativos, dados de utilização de aplicações e dados de nível de segurança. Definir seu nível de telemetria como a telemetria do permite básico do Azure Stack. Os dados coletados, este nível incluem:

- **Informações básicas do dispositivo** que ajuda a fornecer uma compreensão sobre os tipos e as configurações nativas e virtualizadas instâncias do Windows Server 2016 no ecossistema, incluindo:
  - Atributos de computador, como o OEM, modelam,
  - Atributos de sistema de rede, como o número e a velocidade dos adaptadores de rede,
  - Processador e atributos de memória, como o número de núcleos, tamanho da memória,
  - Atributos de armazenamento, como o número de unidades, tipo e tamanho.
- **Funcionalidade de telemetria**, incluindo % de eventos carregados, eventos ignorados e a última hora de carregamento.
- **Informações relacionadas com qualidade** que ajuda a Microsoft a desenvolver uma compreensão básica do sobre a execução do Azure Stack. Um exemplo é a contagem de alertas críticos numa configuração de hardware específico.
- **Dados de compatibilidade**, que ajuda a fornece uma compreensão sobre quais os fornecedores de recursos são instalados num sistema e a máquina virtual e identifica potenciais problemas de compatibilidade.

**2 (avançado)**. Informações adicionais, incluindo: como o sistema operativo e outros serviços do Azure Stack são usados, como executar, dados de confiabilidade avançada e dados de níveis básico e segurança.

**3 (completa)**. Todos os dados necessários para identificar e ajudar a corrigir problemas, além de dados a partir da **segurança**, **básica**, e **avançado** níveis.

> [!NOTE]
> O valor de nível de telemetria de predefinido é 2 (avançado).

Desativar a telemetria do Windows e o Azure Stack desativa a telemetria SQL. Para obter mais informações sobre as implicações das definições de telemetria do Windows Server, fazer referência a [White Paper de telemetria do Windows](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Estes níveis de telemetria aplicam-se apenas aos componentes do Microsoft Azure Stack. Componentes de software não Microsoft e serviços em execução no anfitrião de ciclo de vida de Hardware dos parceiros de hardware do Azure Stack podem se comunicar com os serviços cloud fora destes níveis de telemetria. Deve trabalhar com seu fornecedor de soluções de hardware do Azure Stack para compreender a política de telemetria, e como pode optar ativamente por participar no ou optar por sair.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Ativar ou desativar a telemetria após a implementação

Para ativar ou desativar a telemetria após a implementação, terá de ter acesso para o Privileged ponto final (PEP) que é exposta nas ERCS VMs.
1.  Para ativar: `Set-Telemetry -Enable`
2.  Para desativar o: `Set-Telemetry -Disable`

Detalhes de parâmetro:
> . PARÂMETRO Enable - ativar carregamento de dados de telemetria
> 
> . PARÂMETRO Disable - desativar o carregamento de dados de telemetria  

**Script para ativar a telemetria:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Script para desativar a telemetria:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Passos Seguintes
[Iniciar e parar o ASDK](asdk-start-stop.md)
