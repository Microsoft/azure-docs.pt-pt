---
title: O legado migratório Azure DNS Private Zones para novo modelo de recursos
titleSuffix: Azure DNS
description: Este guia fornece instruções passo a passo sobre como migrar zonas de DNS privadas antigas para o modelo de recursos mais recente
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 72d046cde70d1224eb1fd47f527c9e49c6b002f6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500466"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Azure DNS zonas privadas do legado migratório para novo modelo de recursos

Durante a pré-visualização pública, as zonas privadas de DNS foram criadas utilizando o recurso "dnszones" com propriedade "zoneType" definida para "Private". Estas zonas não serão suportadas após 31 de dezembro de 2019 e devem ser migradas para o modelo de recursos GA que faz uso do tipo de recurso "privateDnsZones" em vez de "dnszones". O processo de migração é simples, e nós fornecemos um script PowerShell para automatizar este processo. Este guia fornece instruções passo a passo para migrar as suas zonas privadas Azure DNS para o novo modelo de recursos.

Para descobrir os recursos dnszones que requerem migração; executar o comando abaixo em Azure CLI.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que instalou a versão mais recente do Azure PowerShell. Para mais informações sobre a Azure PowerShell (Az) e como instalá-la visite https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Certifique-se de que tem o módulo Az.PrivateDns para o Azure PowerShell instalado. Para instalar este módulo, abra uma janela PowerShell elevada (modo administrativo) e insira o comando seguinte

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>O processo de migração é totalmente automatizado e não se espera que cause algum tempo de inatividade. No entanto, se estiver a utilizar zonas privadas Azure DNS (pré-visualização) num ambiente de produção crítico, deverá executar o seguinte processo de migração durante uma janela de tempo de manutenção planeada. Certifique-se de que não modifica a configuração ou os conjuntos de registos de uma zona privada de DNS enquanto executa o script de migração.

## <a name="installing-the-script"></a>Instalação do script

Abra uma janela PowerShell elevada (modo administrativo) e corra seguindo o comando

```powershell
install-script PrivateDnsMigrationScript
```

Introduza "A" quando solicitado para instalar o script

![Instalação do script](./media/private-dns-migration-guide/install-migration-script.png)

Também pode obter manualmente a versão mais recente do script PowerShell em https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>O script de migração não deve ser executado na casca da nuvem de Azure e deve ser executado numa VM ou numa máquina local ligada à internet.

## <a name="running-the-script"></a>Executando o roteiro

Execute o comando seguinte para executar o script

```powershell
PrivateDnsMigrationScript.ps1
```

![Executando o roteiro](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Insira o ID de subscrição e inscreva-se no Azure

Será solicitado que introduza o ID de subscrição contendo as zonas privadas de DNS que pretende migrar. Ser-lhe-á pedido que inscreva-se na sua conta Azure. Preencha o sismo para que o script possa aceder aos recursos privados da zona DNS na subscrição.

![Iniciar sessão no Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Selecione as zonas DNS que pretende migrar

O script com a lista de todas as zonas privadas de DNS na subscrição e pede-lhe que confirme quais as que pretende migrar. Introduza "A" para migrar todas as zonas privadas de DNS. Assim que executar este passo, o script criará novas zonas privadas de DNS utilizando um novo modelo de recursos e copiará os dados para a nova zona DSN. Este passo não irá alterar as suas zonas de DNS privadas existentes em qualquer maneira.

![Selecione zonas DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Mudar a resolução do DNS para as novas zonas DNS

Uma vez que as zonas e registos tenham sido copiados para o novo modelo de recursos, o script irá levá-lo a mudar a resolução DNS para novas zonas DNS. Este passo remove a associação entre as zonas de DNS privadas antigas e as suas redes virtuais. Quando a zona do legado é desvinculada das redes virtuais, as novas zonas DNS criadas em cima do passo assumiriam automaticamente a resolução de DNS para essas redes virtuais.

Selecione 'A' para mudar a resolução DNS para todas as redes virtuais.

![Resolução de nomes de comutação](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Verifique a resolução do DNS

Antes de prosseguir, verifique se a resolução do DNS nas suas zonas de DNS está a funcionar como esperado. Pode iniciar sação nos seus VMs azure e emitir consulta nslookup contra as zonas migratórias para verificar se a resolução de DNS está a funcionar.

![Verificar Resolução de Nome](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Se descobrir que as consultas de DNS não estão a ser resolvidas, aguarde alguns minutos e relemisça as consultas. Se as consultas de DNS estiverem a funcionar como esperado, insira 'Y' quando o script lhe pedir para remover a rede virtual da zona privada de DNS.

![Confirmar Resolução de Nome](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Se por qualquer razão a resolução do DNS contra as zonas migratórias não estiver a funcionar como esperado, insira 'N' acima do passo e o script mudará a resolução de DNS de volta para zonas antigas. Crie um bilhete de apoio e podemos ajudá-lo na migração das suas zonas DNS.

## <a name="cleanup"></a>Limpeza

Este passo irá apagar as zonas de DNS antigas e só deverá ser executado depois de verificar que a resolução do DNS está a funcionar como esperado. Ser-lhe-á solicitado que apague cada zona privada de DNS. Introduza 'Y' em cada 100 horas depois de verificar se a resolução de DNS para aquelas zonas está a funcionar corretamente.

![Limpeza](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Atualize a sua automatização

Se estiver a utilizar a automatização, incluindo modelos, scripts PowerShell ou código personalizado desenvolvido através do SDK, tem de atualizar a sua automatização para utilizar o novo modelo de recursos para as zonas privadas de DNS. Abaixo estão os links para a nova documentação privada do DNS CLI/PS/SDK.
* [Zonas privadas Azure DNS REST API](/rest/api/dns/privatedns/privatezones)
* [Zonas privadas Azure DNS CLI](/cli/azure/ext/privatedns/network/private-dns)
* [Zonas privadas Azure DNS PowerShell](/powershell/module/az.privatedns/)
* [Zonas privadas Azure DNS SDK](/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Precisa de mais ajuda.

Crie um bilhete de apoio se precisar de mais ajuda com o processo de migração ou por qualquer razão que as etapas acima listadas não funcionem para si. Inclua o ficheiro de transcrição gerado pelo script PowerShell com o seu bilhete de apoio.

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada em Azure DNS utilizando [a Azure PowerShell](./private-dns-getstarted-powershell.md) ou [Azure CLI](./private-dns-getstarted-cli.md).

* Leia sobre [alguns cenários comuns de zona privada](./private-dns-scenarios.md) que podem ser realizados com zonas privadas em Azure DNS.

* Para perguntas e respostas comuns sobre zonas privadas em Azure DNS, incluindo comportamentos específicos que pode esperar para determinados tipos de operações, consulte [o DNS PRIVADO FAQ](./dns-faq-private.md).

* Saiba mais sobre as zonas e registos dns, visitando [zonas de DNS e registos.](dns-zones-records.md)

* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.