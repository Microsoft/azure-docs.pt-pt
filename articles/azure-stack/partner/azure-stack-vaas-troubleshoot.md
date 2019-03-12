---
title: Resolver problemas de validação do Azure Stack como serviço | Documentos da Microsoft
description: Resolver problemas de validação como um serviço para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4b212ae884b184ffc60cdc0b3a2181a59efa3904
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760873"
---
# <a name="troubleshoot-validation-as-a-service"></a>Resolver problemas de validação como um serviço

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Seguem-se problemas comuns não relacionado com versões de software e as respectivas soluções.

## <a name="local-agent"></a>Agente local

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>O portal mostra agente local no modo de depuração

O que é provável que uma vez que o agente não consegue enviar heartbeats para o serviço devido a uma ligação de rede instável. Um heartbeat é enviado a cada cinco minutos. Se o serviço não receber um heartbeat durante 15 minutos, em seguida, o serviço considera o agente inativo e testes já não serão agendados no mesmo. Verifique a mensagem de erro na *Agenthost.log* arquivo localizado no diretório onde o agente foi iniciado.

> [!Note]
> Qualquer teste já em execução no agente irá continuar a executar, mas se o heartbeat não é restaurada antes de terminar o teste, em seguida, o agente irá falhar atualizar o estado de teste ou carregar registos. O teste será sempre apresentado como **em execução** e terão de ser cancelada.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Processo de agente na máquina foi encerrado durante a execução de teste. O que esperar?

Se o processo de agente é encerrado maneira brusca por exemplo, máquina reiniciada, processo terminado (CTRL + C na janela de agente é considerado desligamento não), em seguida, o teste que estava a ser executado no mesmo irá continuar a ser apresentado como **em execução**. Se o agente é reiniciado, então o agente irá atualizar o estado do teste para **cancelada**. Se o agente não for reiniciado, o teste é apresentado como **em execução** e têm de cancelar manualmente o teste.

> [!Note]
> Testes de dentro de um fluxo de trabalho estão programados para são executados sequencialmente. **Pendente** testes não serão executados até que os testes no **em execução** Estado no mesmo fluxo de trabalho completo.

## <a name="vm-images"></a>Imagens de VM

### <a name="handle-slow-network-connectivity"></a>Lidar com a conectividade de rede lenta

Pode transferir a imagem do PIR para uma partilha no seu datacenter local. E, em seguida, pode verificar a imagem.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Transferir a imagem do PIR para partilha local, em caso de tráfego de rede lenta

1. Baixe o AzCopy de: [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. Extrair AzCopy.zip e altere o diretório que contém AzCopy.exe

3. Abra o Windows PowerShell a partir de uma linha de elevada. Execute os seguintes comandos:

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare é o caminho de partilha ou o caminho local.

#### <a name="verifying-pir-image-file-hash-value"></a>Verificar o valor de hash de ficheiro de imagem do PIR

Pode usar **Get-HashFile** cmdlet para obter o valor de hash para o repositório de imagens públicas transferidos ficheiros de imagem para verificar a integridade das imagens.

| Nome do Ficheiro | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Ocorre uma falha ao carregar a imagem de VM no `VaaSPreReq` script

Em primeiro lugar, verifique que o ambiente está em bom estado:

1. Partir do DVM / saltar caixa, verifique o que é conseguido iniciar sessão para o portal de administração com as credenciais de administrador.
1. Confirme que não há alertas ou avisos.

Se o ambiente está em bom estado, carrega manualmente as imagens de VM 5 necessárias para execuções de testes de VaaS:

1. Inicie sessão como o administrador de serviço para o portal de administração. Pode encontrar o portal de administração do URL da loja ECE ou o ficheiro de informações do carimbo. Para obter instruções, consulte [parâmetros do ambiente](azure-stack-vaas-parameters.md#environment-parameters).
1. Selecione **mais serviços** > **fornecedores de recursos** > **computação** > **imagens de VM**.
1. Selecione o **+ adicionar** botão na parte superior a **imagens de VM** painel.
1. Modificar ou verificar os valores dos campos seguintes para a primeira imagem VM:
    > [!IMPORTANT]
    > Nem todas as predefinições estiverem corretas para o Item do Marketplace existente.

    | Campo  | Value  |
    |---------|---------|
    | Publicador | MicrosoftWindowsServer |
    | Oferta | WindowsServer |
    | Tipo de SO | Windows |
    | SKU | 2012-R2-Datacenter |
    | Versão | 1.0.0 |
    | URI de Blob de disco do SO | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Selecione o botão **Criar**.
1. Repita para as imagens VM restantes.

As propriedades de todas as imagens VM 5 são os seguintes:

| Publicador  | Oferta  | Tipo de SO | SKU | Versão | URI de Blob de disco do SO |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>Passos Seguintes

- Revisão [notas de versão para a validação como um serviço](azure-stack-vaas-release-notes.md) para alterações em versões mais recentes.