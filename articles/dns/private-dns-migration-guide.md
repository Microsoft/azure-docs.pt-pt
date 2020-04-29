---
title: Legado migratório Azure DNS Zonas Privadas para novo modelo de recursos
titleSuffix: Azure DNS
description: Este guia fornece instruções passo a passo sobre como migrar zonas privadas de DNS para o mais recente modelo de recursos
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: d29885104d6f39a17b5bdeb786cda8f56f58d987
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76939354"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrar legado Azure DNS zonas privadas para novo modelo de recursos

Durante a pré-visualização pública, foram criadas zonas privadas de DNS utilizando recurso "dnszones" com propriedade "zoneType" definida para "Private". Estas zonas não serão suportadas depois de 31 de dezembro de 2019 e devem ser migradas para o modelo de recurso GA que utiliza o tipo de recurso "privateDnsZones" em vez de "zonas de dnszones". O processo de migração é simples, e nós fornecemos um script PowerShell para automatizar este processo. Este guia fornece instruções passo a passo para migrar as suas zonas privadas Azure DNS para o novo modelo de recursos.

Para descobrir os recursos das zonas de dnszones que requerem migração; executar o comando abaixo em Azure CLI.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que instalou a versão mais recente do Azure PowerShell. Para mais informações sobre o Azure PowerShell (Az) e como instalá-lohttps://docs.microsoft.com/powershell/azure/new-azureps-module-az

Certifique-se de que tem o módulo Az.PrivateDns para o Azure PowerShell instalado. Para instalar este módulo, abra uma janela PowerShell elevada (modo administrativo) e introduza o seguinte comando

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>O processo de migração é totalmente automatizado e não se espera que cause qualquer tempo de inatividade. No entanto, se estiver a utilizar zonas privadas Azure DNS (pré-visualização) num ambiente de produção crítico, deverá executar o seguinte processo de migração durante um período de tempo de manutenção planeado. Certifique-se de que não modifica a configuração ou os conjuntos de registos de uma zona privada de DNS enquanto executa o script de migração.

## <a name="installing-the-script"></a>Instalação do script

Abra uma janela PowerShell elevada (modo administrativo) e corra seguindo o comando

```powershell
install-script PrivateDnsMigrationScript
```

Introduza "A" quando solicitado para instalar o script

![Instalação do script](./media/private-dns-migration-guide/install-migration-script.png)

Também pode obter manualmente a versão mais recente do script PowerShell emhttps://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>O roteiro de migração não deve ser executado em casca de nuvem Azure e deve ser executado numa VM ou numa máquina local ligada à internet.

## <a name="running-the-script"></a>Executando o script

Executar o seguinte comando para executar o script

```powershell
PrivateDnsMigrationScript.ps1
```

![Executando o script](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Introduza o ID de subscrição e inscreva-se no Azure

Será solicitado a introduzir o ID de subscrição contendo as zonas privadas de DNS que pretende migrar. Será convidado a inscrever-se na sua conta Azure. Complete o sign-in para que o script possa aceder aos recursos privados da zona DNS na subscrição.

![Iniciar sessão no Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Selecione as zonas DNS que pretende migrar

O script com a lista de todas as zonas privadas de DNS na subscrição e solicita-lhe que confirme quais as que pretende migrar. Insira "A" para migrar todas as zonas privadas de DNS. Assim que executar este passo, o script criará novas zonas privadas de DNS usando um novo modelo de recurso e copiará os dados para a nova zona DSN. Este passo não alterará as suas zonas privadas de DNS existentes.

![Selecione zonas DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Mudar a resolução do DNS para as novas zonas de DNS

Uma vez que as zonas e registos tenham sido copiados para o novo modelo de recursos, o script irá pedir-lhe que mude a resolução DNS para novas zonas DeNs. Este passo remove a associação entre as zonas privadas de DNS e as suas redes virtuais. Quando a zona do legado não está ligada às redes virtuais, as novas zonas de DNS criadas em passo acima assumiriam automaticamente a resolução dNS para essas redes virtuais.

Selecione 'A' para mudar a resolução DNS para todas as redes virtuais.

![Resolução de nomes de comutação](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Verifique a resolução do DNS

Antes de prosseguir, verifique se a resolução do DNS nas suas zonas de DNS está a funcionar como esperado. Pode iniciar sessão nos seus VMs azuis e emitir consulta de nslookup contra as zonas migradas para verificar se a resolução de DNS está a funcionar.

![Verificar resolução de nomes](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Se descobrir que as consultas do DNS não estão a resolver, espere alguns minutos e reexperimente as consultas. Se as consultas de DNS estiverem a funcionar como esperado, introduza 'Y' quando o script lhe pedir para remover a rede virtual da zona privada de DNS.

![Confirmar Resolução de Nomes](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Se por qualquer razão a resolução do DNS contra as zonas migradas não estiver a funcionar como esperado, introduza 'N' em passo acima e o script mudará a resolução dNS de volta para zonas antigas. Crie um bilhete de apoio e podemos ajudá-lo na migração das suas zonas de DNS.

## <a name="cleanup"></a>Limpeza

Este passo eliminará as zonas dNS antigas e só deve ser executado depois de verificar que a resolução do DNS está a funcionar como esperado. Ser-lhe-á pedido que apague cada zona privada de DNS. Introduza 'Y' em todas as solicitações depois de verificar se a resolução de DNS para essa zona está a funcionar corretamente.

![Limpeza](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Atualizar A sua automatização

Se estiver a utilizar automação, incluindo modelos, scripts PowerShell ou código personalizado desenvolvido sem Recurso SDK, tem de atualizar a sua automatização para utilizar o novo modelo de recursos para as zonas privadas de DNS. Abaixo estão os links para a nova documentação privada DNS CLI/PS/SDK.
* [Zonas privadas Azure DNS REST API](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [Zonas privadas Azure DNS CLI](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [Zonas privadas Azure DNS PowerShell](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [Zonas privadas Azure DNS SDK](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Precisa de mais ajuda

Crie um bilhete de apoio se precisar de mais ajuda com o processo de migração ou por qualquer razão para que os passos acima listados não funcionem para si. Inclua o ficheiro de transcrição gerado pelo script PowerShell com o seu bilhete de suporte.

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada em Azure DNS utilizando [o Azure PowerShell](./private-dns-getstarted-powershell.md) ou [o Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários de zona privada](./private-dns-scenarios.md) comum que podem ser realizados com zonas privadas em DNS Azure.

* Para perguntas e respostas comuns sobre zonas privadas em DNS Azure, incluindo comportamentos específicos que pode esperar para determinados tipos de operações, consulte o [DNS faQ privado](./dns-faq-private.md).

* Conheça as zonas e registos dNS visitando [zonas de DNS e a visão geral](dns-zones-records.md)dos registos .

* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
