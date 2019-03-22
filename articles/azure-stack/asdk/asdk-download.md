---
title: Baixe e extraia o Azure Stack Development Kit (ASDK) | Documentos da Microsoft
description: Descreve como transferir e extrair o Azure Stack Development Kit (ASDK).
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
ms.lastreviewed: 08/10/2018
ms.openlocfilehash: f48c3c9691170df6d460bfe9a6da0e02e4be32c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077705"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Baixe e extraia o Azure Stack Development Kit (ASDK)
Depois de assegurar que seu computador de anfitrião do kit de desenvolvimento cumpre os requisitos básicos para instalar o ASDK, a próxima etapa é baixar e extrair o pacote de implementação para obter o Cloudbuilder.vhdx ASDK.

## <a name="download-the-asdk"></a>Transferir o ASDK
1. Antes de iniciar a transferência, certifique-se de que o computador cumpre os seguintes pré-requisitos:

   - O computador tem de ter, pelo menos, 60 GB de espaço em disco disponível em quatro parte, idênticas lógicas unidades de disco rígido livre além para o disco do sistema operativo.
   - [.NET framework 4.6 (ou uma versão posterior)](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) tem de ser instalado.

2. [Vá para a página de introdução ao](https://azure.microsoft.com/overview/azure-stack/try/?v=try) onde pode transferir o Development Kit do Azure Stack, forneça os detalhes e, em seguida, clique em **submeter**.
3. Transferir e executar o [Verificador de implementação para o Azure Stack Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) script do Verificador de pré-requisitos. Este script autónomo aborda as verificações de pré-requisitos feitas pela configuração para o Azure Stack Development Kit. Fornece uma forma para confirmar que está a cumprir os requisitos de hardware e software, antes de transferir o pacote maior para o Azure Stack Development Kit.
4. Sob **baixar o software**, clique em **Development Kit do Azure Stack**.

   > [!NOTE]
   > O download ASDK (AzureStackDevelopmentKit.exe) é cerca de 10GB.

## <a name="extract-the-asdk"></a>Extrair o ASDK
1. Depois de concluída a transferência, clique em **executar** para iniciar o ASDK Self-extractor (AzureStackDevelopmentKit.exe).
2. Rever e aceitar o contrato de licença apresentado dos **contrato de licença** página do Assistente de Self-Extractor e clique em **próxima**.
3. Reveja as informações de declaração de privacidade apresentadas na **aviso importante** página do Assistente de Self-Extractor e clique em **próxima**.
4. Selecione a localização de ficheiros de configuração do Azure Stack a ser extraído no **selecionar localização de destino** página do Assistente de Self-Extractor e clique em **próxima**. A localização predefinida é *pasta atual*\Azure Stack Development Kit. 
5. Reveja a localização de destino resumo sobre o **pronto para extrair** página do Assistente de Self-Extractor e, em seguida, clique **extrair** para extrair o CloudBuilder.vhdx (aproximadamente 28 GB) e Ficheiros de ThirdPartyLicenses.rtf. Este processo demora algum tempo a concluir.
6. Copiar ou mover o ficheiro de CloudBuilder.vhdx na raiz da unidade C:\ (C:\CloudBuilder.vhdx) no computador anfitrião ASDK.

> [!NOTE]
> Depois de extrair os ficheiros, pode eliminar o. EXE e. Ficheiros BIN para recuperar espaço no disco rígido. Em alternativa, pode fazer backup desses arquivos, de modo a que não seja necessário transferir os ficheiros novamente, se precisar de voltar a implementar o ASDK.


## <a name="next-steps"></a>Passos Seguintes
[Preparar o computador do anfitrião ASDK](asdk-prepare-host.md)