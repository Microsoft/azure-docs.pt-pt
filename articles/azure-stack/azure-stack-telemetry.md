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
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: comartin
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 1977ced65b6dd62a023a79ce8949a8b428d2f965
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760363"
---
# <a name="azure-stack-telemetry"></a>Telemetria de pilha do Azure

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Telemetria de pilha do Azure carrega automaticamente os dados de sistema para a Microsoft via a experiência do usuário conectado. As equipes da Microsoft utilizam os dados que recolhe de telemetria do Azure Stack para melhorar as experiências dos clientes. Estes dados também são utilizados para segurança, estado de funcionamento, qualidade e análise de desempenho.

Para um operador do Azure Stack, telemetria pode fornecer informações valiosas para implementações em empresas e dá-lhe uma voz que ajuda a forma as futuras versões do Azure Stack.

> [!NOTE]
> Também pode configurar o Azure Stack para reencaminhar as informações de utilização para o Azure para faturação. Isto é necessário para clientes com vários nós do Azure Stack que optam por faturação de pagamento-como-utiliza. Relatórios de utilização é controlada de forma independente de telemetria e não é necessária para os clientes de vários nós que escolher o modelo de capacidade ou para usuários do Kit de desenvolvimento do Azure Stack. Nestes cenários, relatórios de utilização podem ser desativado [utilizando o script de Registro](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Telemetria de pilha do Azure baseia-se o componente telemetria e experiência do usuário conectado Windows Server 2016, que utiliza a [rastreio de eventos para Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging tecnologia para recolher e armazenar eventos e dados. Componentes de pilha do Azure utilizam a mesma tecnologia para publicar eventos e os dados coletados com o log de eventos do sistema de operativo pública e APIs de rastreio. Exemplos destes componentes do Azure Stack incluem estes fornecedores: Recurso, recurso de armazenamento, recursos de monitorização de rede e atualizar o recurso. O componente ligado a experiência de utilizador e telemetria encripta os dados com SSL e utiliza a afixação de certificado de mensagens em fila para transmitir dados através de HTTPS para o serviço de gestão de dados da Microsoft.

> [!IMPORTANT]
> Para ativar o fluxo de dados de telemetria, porta 443 (HTTPS) tem de estar aberta na sua rede. O componente ligado a experiência de utilizador e telemetria liga ao serviço de gestão de dados da Microsoft em https://v10.vortex-win.data.microsoft.com. O componente ligado a experiência de utilizador e telemetria liga-se também ao https://settings-win.data.microsoft.com para transferir informações de configuração.

## <a name="privacy-considerations"></a>Considerações de privacidade

O serviço ETW encaminha dados de telemetria de volta para o armazenamento de nuvem protegida. O princípio de privilégio mínimo orienta o acesso aos dados de telemetria. Apenas as equipas da Microsoft com uma necessidade de negócio válida recebem acesso aos dados de telemetria. A Microsoft não partilha dados de pessoal de cliente com terceiros, exceto a critério do cliente ou para os fins de limitado descritos na [declaração de privacidade do Microsoft](https://privacy.microsoft.com/PrivacyStatement). Os relatórios de negócio que são partilhados com os OEMs e parceiros incluem dados agregados, anónimos. Decisões de partilha de dados são feitos por uma equipe interna do Microsoft, incluindo partes interessadas de gestão de dados, legais e privacidade.

A Microsoft acredita no e práticas minimização de informações. Esforçamo-nos reunir apenas as informações que não é necessário e armazená-lo para apenas tempo necessário para fornecer um serviço ou para análise. Muitas das informações sobre como o sistema do Azure Stack e serviços do Azure estão a funcionar é eliminada dentro de seis meses. Resumidos ou dados agregados serão mantidos para um período mais longo.

Estamos cientes de que a privacidade e segurança de informações de clientes é importante.  Microsoft assume uma abordagem abrangente e uma execução ponderados a privacidade dos clientes e a proteção dos dados dos clientes no Azure Stack. Os administradores de TI tem controlos para personalizar funcionalidades e definições de privacidade em qualquer altura. Nosso compromisso para com transparência e a confiança é claro:

- Estamos abertos com os clientes sobre os tipos de dados, que podemos reunir.
- Colocamos os clientes empresariais no controle — eles podem personalizar as suas próprias definições de privacidade.
- Colocamos segurança e privacidade do cliente pela primeira vez.
- Estamos transparentes sobre como os dados de telemetria são usados.
- Utilizamos os dados de telemetria para melhorar as experiências dos clientes.

Microsoft não a pretende recolher dados confidenciais, como números de cartão de crédito, nomes de utilizador e palavras-passe, endereços de e-mail ou informações confidenciais semelhantes. Se determinarmos que informações confidenciais foi recebidas inadvertidamente, podemos eliminá-lo.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exemplos de como a Microsoft usa os dados de telemetria

Telemetria desempenha um papel importante nos ajudar a identificar e corrigir problemas de confiabilidade essencial em implementações de cliente e configurações rapidamente. Informações de dados de telemetria podem ajudar a identificar problemas com os serviços ou configurações de hardware. Capacidade da Microsoft para obter esses dados de clientes e melhoramentos do ecossistema, gera a barra de qualidade de soluções integradas do Azure Stack.

Telemetria também ajuda a Microsoft para compreender melhor como os clientes implementar componentes, utilizar as funcionalidades e utilizar os serviços para alcançar seus objetivos comerciais. Estas informações ajudam a atribuir prioridades a investimentos de engenharia nas áreas que podem afetar diretamente as experiências de cliente e cargas de trabalho.

Alguns exemplos incluem a utilização do cliente de contentores, armazenamento e as configurações de rede que estão associadas a funções do Azure Stack. Também utilizaremos as informações para implementar melhoramentos e inteligência em gestão do Azure Stack e soluções de monitorização. Esses aprimoramentos que seja mais fácil para os clientes diagnosticar problemas e poupar dinheiro, tornando menos suporte chama para a Microsoft.

## <a name="manage-telemetry-collection"></a>Gerir coleção de telemetria

Não temos mais recomendado desativar a telemetria na sua organização. No entanto, em alguns cenários Isto poderá ser necessário.

Nestes cenários, pode configurar o nível de telemetria enviado à Microsoft ao utilizar as definições de registo antes de implementar o Azure Stack ou com os pontos de extremidade de telemetria, depois de implementar o Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Recolha de dados e níveis de telemetria

Antes de alterar as definições de telemetria, deve compreender os níveis de telemetria e que dados são recolhidos em cada nível.

As definições de telemetria são agrupadas em quatro níveis (0 a 3), que são cumulativas e categorizadas como os seguintes passos:

**0 (segurança)**</br>
Apenas dados de segurança. Informações necessárias para manter o sistema operacional seguro. Isto inclui dados sobre as definições de componente de telemetria e experiência do usuário conectado e o Windows Defender. Sem telemetria específica para o Azure Stack é emitida neste nível.

**1 (básico)**</br>
Os dados de segurança e dados de estado de funcionamento básico e qualidade. Informações básicas do dispositivo, incluindo: dados relacionados com a qualidade, compatibilidade de aplicativos, dados de utilização da aplicação e dados a partir da **segurança** nível. Definir seu nível de telemetria como a telemetria do permite básico do Azure Stack. Os dados coletados, este nível incluem:

- *Informações básicas do dispositivo* que permite maior compreensão sobre os tipos e as configurações nativas e virtuais instâncias do Windows Server 2016 no ecossistema. Isto inclui:

  - Atributos de computador, como o OEM e o modelo.
  - Atributos, como o número de adaptadores de rede e a velocidade de rede.
  - Atributos de processador e memória, como o número de núcleos e a quantidade de memória instalada.
  - Atributos de armazenamento, como o número de unidades, o tipo de unidade e o tamanho de unidade.

- *Funcionalidade de telemetria*, incluindo a percentagem de eventos carregados, eventos ignorados e os dados de última hora de carregamento.
- *Informações relacionadas com qualidade* que ajuda a Microsoft a desenvolver uma compreensão básica do sobre a execução do Azure Stack. Por exemplo, a contagem de alertas críticos numa configuração de hardware específico.
- *Dados de compatibilidade* que ajuda a fornecer uma compreensão sobre quais os fornecedores de recursos são instalados num sistema e de uma máquina virtual. Identifica potenciais problemas de compatibilidade.

**2 (avançado)**</br>
Informações adicionais, incluindo: como o sistema operativo e os serviços do Azure Stack são usados, como tais serviços executam, confiabilidade avançada dados e dados a partir do **Security** e **básica** níveis.

> [!NOTE]
> Esta é a predefinição de telemetria.

**3 (completa)**</br>
Todos os dados necessários para identificar e ajudar a corrigir problemas, além de dados a partir da **segurança**, **básica**, e **avançado** níveis.

> [!IMPORTANT]
> Estes níveis de telemetria aplicam-se apenas aos componentes do Microsoft Azure Stack. Componentes de software não Microsoft e serviços em execução no anfitrião de ciclo de vida de Hardware dos parceiros de hardware do Azure Stack podem se comunicar com os serviços cloud fora destes níveis de telemetria. Deve trabalhar com seu fornecedor de soluções de hardware do Azure Stack para compreender a política de telemetria, e como pode optar ativamente por participar no ou optar por sair.

Desativar a telemetria do Windows e o Azure Stack também Desabilita a telemetria SQL. Para obter mais informações sobre as implicações das definições de telemetria do Windows Server, consulte a [White Paper de telemetria do Windows](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: definir o nível de telemetria no registo do Windows

Pode usar o Editor de registo do Windows para definir manualmente o nível de telemetria no computador anfitrião físico antes de implementar o Azure Stack. Se uma política de gestão já existir, como a diretiva de grupo, ele substitui esta definição de registo.

Antes de implementar o Azure Stack do anfitrião do kit de desenvolvimento, arrancar CloudBuilder.vhdx e execute o seguinte script numa janela do PowerShell elevada:

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

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK e com vários nós: Ativar ou desativar a telemetria após a implementação

Para ativar ou desativar a telemetria após a implementação, terá de ter acesso para o Privileged ponto final (PEP) que é exposta nas ERCS VMs.

1. Para ativar: `Set-Telemetry -Enable`
2. Para desativar o: `Set-Telemetry -Disable`

Detalhes de parâmetro:
> . PARÂMETRO Enable - ativar carregamento de dados de telemetria</br>
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

[Registar o Azure Stack com o Azure](azure-stack-registration.md)