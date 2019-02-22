---
title: Remediar problemas de certificado para o Azure Stack | Documentos da Microsoft
description: Utilize o Verificador de preparação do Azure Stack para analisar e resolver problemas de certificado.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: 0a5c0204909a3fa6730a5e852fb24b2213e093a1
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650585"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Corrigir problemas comuns para certificados PKI de pilha do Azure

As informações neste artigo podem ajudá-lo a compreender e resolver problemas comuns para certificados PKI de pilha do Azure. Pode descobrir problemas quando utiliza a ferramenta do Verificador de preparação do Azure Stack para [validar certificados PKI de pilha do Azure](azure-stack-validate-pki-certs.md). A ferramenta verifica para garantir que os certificados de cumprir os requisitos de PKI de uma implementação do Azure Stack e a rotação de segredo do Azure Stack e regista os resultados numa [report.json ficheiro](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>Encriptação de PFX

**Falha de** -encriptação PFX není TripleDES SHA1.

**Remediação** -exportar PFX ficheiros com **TripleDES SHA1** encriptação. Esta é a predefinição para todos os clientes do Windows 10 ao exportar a partir do certificado de snap-in ou utilizando `Export-PFXCertificate`.

## <a name="read-pfx"></a>Leia o PFX

**Aviso** -palavra-passe só protege as informações privadas do certificado.  

**Remediação** -ficheiros PFX exportar com a definição opcional para **ativar privacidade de certificado**.  

**Falha de** -inválido de ficheiro PFX.  

**Remediação** -exportar o certificado utilizando os passos [certificados de preparar o Azure Stack PKI para a implementação](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmo de assinatura

**Falha de** -algoritmo de assinatura é SHA1.

**Remediação** -utilize os passos na assinatura de geração de pedido para voltar a gerar o certificado de assinatura pedido (CSR) com o algoritmo de assinatura de SHA256 de certificados de Azure Stack. Em seguida, volte a submeter o CSR para a autoridade de certificado a emitir o certificado.

## <a name="private-key"></a>Chave privada

**Falha de** -a chave privada está em falta ou não contém o atributo de máquina Local.  

**Remediação** - a partir do computador que gerou o CSR, exportar o certificado utilizando os passos [certificados de preparar o Azure Stack PKI para a implementação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment). Estes passos incluem a exportação do arquivo de certificados de computador local.

## <a name="certificate-chain"></a>Cadeia de certificados

**Falha de** -cadeia de certificados não está concluída.  

**Remediação** -certificados devem conter uma cadeia de certificados completa. Exportar o certificado utilizando os passos em [certificados PKI de pilha de Azure preparar para implantação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) e selecione a opção **incluir todos os certificados no caminho de certificação se possível.**

## <a name="dns-names"></a>Nomes DNS

**Falha de** -o DNSNameList no certificado não contém o nome de ponto final de serviço do Azure Stack, ou uma correspondência de caráter universal válido. Correspondências de caráter universal só são válidas para o espaço de nomes mais à esquerda do nome DNS. Por exemplo, _*. region.domain.com_ só é válido para *portal.region.domain.com*, e não _*. table.region.domain.com_.

**Remediação** -utilizar os passos na geração de pedido para voltar a gerar o CSR com os nomes DNS corretos de assinatura de certificados de Azure Stack para suportar pontos de extremidade do Azure Stack. Volte a submeter o CSR para uma autoridade de certificação e, em seguida, siga os passos em [certificados de preparar o Azure Stack PKI para a implementação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) para exportar o certificado da máquina que gerou o CSR.  

## <a name="key-usage"></a>Utilização de chaves

**Falha de** – utilização de chaves está em falta assinatura digital ou cifragem de chaves ou utilização de chave avançada tem em falta autenticação de servidor ou a autenticação de cliente.  

**Remediação** -utilize os passos em [geração de pedido de assinatura de certificados do Azure Stack](azure-stack-get-pki-certs.md) para voltar a gerar o CSR com os atributos de utilização de chave corretos. Volte a submeter o CSR para a autoridade de certificação e confirme que um modelo de certificado não está substituindo a utilização de chave no pedido.

## <a name="key-size"></a>Tamanho da chave

**Falha de** -tamanho de chave é menor do que 2048.

**Remediação** -utilize os passos em [geração de pedido de assinatura de certificados do Azure Stack](azure-stack-get-pki-certs.md) para voltar a gerar o CSR com o comprimento de chave correto (2048) e, em seguida, volte a submeter o CSR para a autoridade de certificação.

## <a name="chain-order"></a>Ordem da cadeia

**Falha de** -a ordem de cadeia de certificados é incorreta.  

**Remediação** -exportar o certificado utilizando os passos [certificados de preparar o Azure Stack PKI para implantação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) e selecione a opção **incluir todos os certificados no caminho de certificação se possível.** Certifique-se de que apenas o certificado de folha está selecionado para exportação.

## <a name="other-certificates"></a>Outros certificados

**Falha de** -pacote o PFX contém certificados que não sejam o certificado de folha ou parte da cadeia de certificados.  

**Remediação** -exportar o certificado utilizando os passos [certificados de preparar o Azure Stack PKI para implantação](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)e selecione a opção **incluir todos os certificados no caminho de certificação se possível.** Certifique-se de que apenas o certificado de folha está selecionado para exportação.

## <a name="fix-common-packaging-issues"></a>Corrigir problemas comuns de empacotamento

O **AzsReadinessChecker** contém um cmdlet de programa auxiliar chamado `Repair-AzsPfxCertificate`, que pode importar e, em seguida, exportar um PFX de ficheiros para corrigir problemas de empacotamento comuns, incluindo:

- *Encriptação de PFX* není TripleDES SHA1.
- *Chave privada* está em falta o atributo de máquina Local.
- *Cadeia de certificados* está incompleta ou errado. A máquina local tem de conter a cadeia de certificados se o pacote PFX não existir.
- *Outros certificados*

`Repair-AzsPfxCertificate` não pode ajudá-lo se precisa gerar um novo CSR e volte a emitir um certificado.

### <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos seguintes aspetos estejam implementados no computador no qual a ferramenta é executada:

- Windows 10 ou Windows Server 2016, com ligação à internet.
- PowerShell 5.1 ou posterior. Para verificar a sua versão, execute o seguinte cmdlet do PowerShell e, em seguida, reveja os *principais* e *pequenas* versões:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Configurar [PowerShell para o Azure Stack](azure-stack-powershell-install.md).
- Baixe a versão mais recente dos [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) ferramenta.

### <a name="import-and-export-an-existing-pfx-file"></a>Importar e exportar um ficheiro PFX existente

1. Num computador que cumpra os pré-requisitos, abra uma linha do PowerShell de administrador e, em seguida, execute o seguinte comando para instalar o AzsReadinessChecker:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Prompt do PowerShell, execute o seguinte cmdlet para definir a palavra-passe PFX. Substitua `PFXpassword` com a palavra-passe real:

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. A partir da linha de comandos do PowerShell, execute o seguinte para exportar um novo ficheiro PFX:

   - Para `-PfxPath`, especifique o caminho para o ficheiro PFX está a trabalhar com. No exemplo a seguir, o caminho é `.\certificates\ssl.pfx`.
   - Para `-ExportPFXPath`, especifique a localização e o nome do ficheiro PFX para exportação. No exemplo a seguir, o caminho é `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Depois de concluída a ferramenta, reveja o resultado de êxito:

   ```powershell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Passos Seguintes

- [Aceda aqui para saber mais sobre a segurança do Azure Stack](azure-stack-rotate-secrets.md).
