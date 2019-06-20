---
title: Migrar herdados as zonas privadas do DNS do Azure para o novo modelo de recursos
description: Este guia fornece instruções passo a passo sobre como migrar herdadas zonas privadas do DNS para o modelo de recursos mais recentes
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: e7ebbf35cd572601f02a69930b58811686a92c86
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276098"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrar herdadas zonas privadas do DNS do Azure para o novo modelo de recursos

Lançamos um novo modelo de recurso/API para zonas privadas do DNS do Azure como parte da versão de atualização de pré-visualização. Atualização de pré-visualização fornece novas funcionalidades e remove várias limitações e restrições da pré-visualização pública inicial. No entanto, esses benefícios não estão disponíveis em zonas privadas do DNS que foram criadas utilizando a API herdada. Para obter os benefícios da nova versão, terá de migrar os recursos de zona DNS privados legados para o novo modelo de recurso. O processo de migração é simples e fornecemos um script do PowerShell para automatizar este processo. Este guia fornece instruções passo a passo para migrar suas zonas privadas do DNS do Azure para o novo modelo de recursos.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que instalou a versão mais recente do Azure PowerShell. Para obter mais informações sobre o Azure PowerShell (Az) e como instalá-lo visita https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Certifique-se de que o módulo de Az.PrivateDns para o Azure PowerShell instalada. Para instalar este módulo, abra uma janela elevada do PowerShell (modo administrativo) e introduza o seguinte comando

```powershell
Install-Module -Name Az.PrivateDns -AllowPrerelease
```

>[!IMPORTANT]
>O processo de migração é totalmente automatizado e não se espera que causar qualquer período de inatividade. No entanto, se estiver a utilizar zonas privadas do DNS do Azure (pré-visualização) num ambiente de produção críticas devem executar abaixo o processo de migração durante uma janela de tempo de manutenção planeada. Certifique-se de que não a modifique a configuração ou conjuntos de registos de zonas privadas do DNS enquanto estiver a executar o script de migração.

## <a name="installing-the-script"></a>Instalar o script

Abra uma janela elevada do PowerShell (modo administrativo) e execute o comando seguinte

```powershell
install-script PrivateDnsMigrationScript
```

Introduza "A" quando lhe for pedido para instalar o script

![Instalar o script](./media/private-dns-migration-guide/install-migration-script.png)

Pode obter a versão mais recente do script do PowerShell no também manualmente https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

## <a name="running-the-script"></a>Executar o script

Execute a seguinte comando para executar o script

```powershell
PrivateDnsMigrationScript.ps1
```

![Executar o script](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Introduza o ID de subscrição e início de sessão para o Azure

Será solicitado para introduzir o ID de subscrição que contém as zonas privadas do DNS que pretende migrar. Será solicitado a iniciar sessão na sua conta do Azure. Conclua o início de sessão para que o script pode acessar os recursos de zona DNS privados na subscrição.

![Iniciar sessão no Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Selecione as zonas DNS que pretende migrar

O script com o obter a lista de todas as zonas DNS privadas na subscrição e pedir-lhe para confirmar que aqueles que pretende migrar. Introduza "A" para migrar todas as zonas privadas do DNS. Depois de executar este passo, o script irá criar novas zonas privadas do DNS com o novo modelo de recursos e copiar os dados para a nova zona DSN. Este passo não irá alterar suas zonas privadas do DNS existentes no mesmo assim.

![Selecione as zonas DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Mudar a resolução de DNS para as novo zonas DNS

Depois da zonas e registos foram copiados para o novo modelo de recursos, o script irá solicitar-lhe para mudar a resolução DNS para o novo zonas DNS. Este passo remove a associação entre zonas privadas do DNS herdadas e redes virtuais. Quando a zona herdada é desassociada das redes virtuais, as zonas DNS novo, criadas nos passos acima seriam automaticamente têm sobre a resolução DNS para essas redes virtuais.

Selecione "A" para mudar a resolução DNS para todas as redes virtuais.

![Alternância de resolução de nomes](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Certifique-se a resolução DNS

Antes de prosseguir, certifique-se de que a resolução de DNS em suas zonas DNS está a funcionar conforme esperado. Pode iniciar sessão nas VMS do azure e consulta de nslookup problema contra as zonas migradas para verificar que a resolução de DNS está a funcionar.

![Certifique-se de resolução de nomes](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Se achar que não são resolução de consultas DNS, aguarde alguns minutos e repita as consultas. Se as consultas DNS estão a funcionar conforme esperado, introduza "Y", quando o script pede-lhe para remover a rede virtual da zona DNS privada.

![Confirmar a resolução de nomes](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Se por qualquer motivo DNS resolução contra as zonas migradas não está a funcionar conforme esperado, introduza "n" nos passos acima e o script irá mudar a resolução DNS para zonas de legado. Criar um pedido de suporte e podemos pode ajudá-lo com a migração das suas zonas DNS.

## <a name="cleanup"></a>Limpeza

Este passo irá eliminar as zonas DNS herdadas e deve ser executado apenas depois de verificar se a resolução de DNS está a funcionar conforme esperado. Será solicitado para eliminar cada zona DNS privada. Introduza "Y" cada linha de comandos depois de verificar a resolução de DNS para que as zonas que está a funcionar corretamente.

![Limpeza](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Atualizar a automatização

Se estiver usando a automação, incluindo modelos, scripts do PowerShell ou desenvolvidos com o SDK de código personalizado, tem de atualizar a automatização para utilizar o novo modelo de recursos para as zonas privadas do DNS. Seguem-se as ligações para a nova documentação da CLI do DNS/PS/SDK privada.
* [REST API de zonas de DNS do Azure privado](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Zonas privadas do Azure do DNS da CLI](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Zonas privadas do Azure do DNS do PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Zonas privadas do Azure do DNS SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Precisa de mais ajuda

Crie um pedido de suporte se necessitar de mais ajuda com o processo de migração ou por qualquer motivo, as etapas listadas acima não funcionarem para. Inclua o arquivo de transcrição gerado pelo script do PowerShell com o seu pedido de suporte.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como criar uma zona privada no DNS do Azure com [do Azure PowerShell](./private-dns-getstarted-powershell.md) ou [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia sobre alguns comum [cenários de zona privada](./private-dns-scenarios.md) que podem ser conseguidos com as zonas privadas no DNS do Azure.

* Para perguntas e respostas sobre zonas privadas no DNS do Azure comuns, incluindo o comportamento específico pode esperar para determinados tipos de operações, consulte [FAQ do DNS privado](./dns-faq-private.md).

* Saiba mais sobre zonas e registos DNS ao visitar [zonas e registos de descrição geral do DNS](dns-zones-records.md).

* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
