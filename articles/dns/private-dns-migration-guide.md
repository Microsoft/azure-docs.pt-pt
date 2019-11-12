---
title: Migrando Zonas Privadas do DNS do Azure herdados para o novo modelo de recurso
description: Este guia fornece instruções passo a passo sobre como migrar zonas DNS privadas herdadas para o modelo de recurso mais recente
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 9f52a568d42fa23a40a396311955626a1fa0073b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931263"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrando zonas privadas do DNS do Azure herdadas para o novo modelo de recurso

Durante a visualização pública, as zonas DNS privadas foram criadas usando o recurso "dnszones" com a propriedade "zonetype" definida como "Private". Essas zonas não terão suporte após 31 de dezembro de 2019 e devem ser migradas para o modelo de recurso de GA, que usa o tipo de recurso "privateDnsZones" em vez de "dnszones". O processo de migração é simples e fornecemos um script do PowerShell para automatizar esse processo. Este guia fornece instruções passo a passo para migrar suas zonas privadas do DNS do Azure para o novo modelo de recurso.

Para descobrir os recursos de dnszones que exigem migração; Execute o comando abaixo no CLI do Azure.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você instalou a versão mais recente do Azure PowerShell. Para obter mais informações sobre Azure PowerShell (AZ) e como instalá-lo, visite https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Verifique se você tem o módulo AZ. PrivateDns para o Azure PowerShell instalado. Para instalar este módulo, abra uma janela do PowerShell com privilégios elevados (modo administrativo) e digite o seguinte comando

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>O processo de migração é totalmente automatizado e não se espera que cause nenhum tempo de inatividade. No entanto, se você estiver usando zonas privadas do DNS do Azure (versão prévia) em um ambiente de produção crítico, deverá executar o processo de migração a seguir durante uma janela de tempo de manutenção planejada. Certifique-se de não modificar a configuração ou os conjuntos de registros de uma zona DNS privada enquanto estiver executando o script de migração.

## <a name="installing-the-script"></a>Instalando o script

Abra uma janela do PowerShell com privilégios elevados (modo administrativo) e execute o comando a seguir

```powershell
install-script PrivateDnsMigrationScript
```

Insira "A" quando for solicitado a instalar o script

![Instalando o script](./media/private-dns-migration-guide/install-migration-script.png)

Você também pode obter manualmente a versão mais recente do script do PowerShell em https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>O script de migração não deve ser executado no Azure cloud Shell e deve ser executado em uma VM ou máquina local conectada à Internet.

## <a name="running-the-script"></a>Executando o script

Execute o comando a seguir para executar o script

```powershell
PrivateDnsMigrationScript.ps1
```

![Executando o script](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Insira a ID da assinatura e entre no Azure

Você será solicitado a inserir a ID de assinatura que contém as zonas DNS privadas que você pretende migrar. Você será solicitado a entrar em sua conta do Azure. Conclua a entrada para que o script possa acessar os recursos de zona DNS privados na assinatura.

![Iniciar sessão no Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Selecione as zonas DNS que você deseja migrar

O script Obtém a lista de todas as zonas DNS privadas na assinatura e solicita que você confirme quais delas deseja migrar. Insira "A" para migrar todas as zonas DNS privadas. Depois de executar essa etapa, o script criará novas zonas DNS privadas usando o novo modelo de recurso e copiará os dados para a nova zona DSN. Essa etapa não alterará suas zonas DNS privadas existentes de qualquer forma.

![Selecionar zonas DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Alternando a resolução DNS para as novas zonas DNS

Depois que as zonas e os registros tiverem sido copiados para o novo modelo de recurso, o script solicitará que você alterne a resolução de DNS para novas zonas DNS. Esta etapa remove a associação entre as zonas DNS privadas herdadas e suas redes virtuais. Quando a zona herdada for desvinculada das redes virtuais, as novas zonas DNS criadas na etapa acima assumirão automaticamente a resolução de DNS para essas redes virtuais.

Selecione ' A ' para alternar a resolução de DNS para todas as redes virtuais.

![Alternando a resolução de nomes](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Verificar a resolução de DNS

Antes de continuar, verifique se a resolução DNS em suas zonas DNS está funcionando conforme o esperado. Você pode entrar em suas VMs do Azure e emitir a consulta NSLOOKUP nas zonas migradas para verificar se a resolução DNS está funcionando.

![Verificar resolução de nome](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Se você achar que as consultas DNS não estão resolvendo, aguarde alguns minutos e repita as consultas. Se as consultas DNS estiverem funcionando conforme o esperado, insira ' Y ' quando o script solicitar que você remova a rede virtual da zona DNS privada.

![Confirmar resolução de nome](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Se, devido a qualquer motivo, a resolução de DNS em relação às zonas migradas não estiver funcionando conforme o esperado, insira ' n' na etapa acima e o script mudará a resolução de DNS de volta para zonas herdadas. Crie um tíquete de suporte e poderemos ajudá-lo com a migração de suas zonas DNS.

## <a name="cleanup"></a>Limpeza

Esta etapa excluirá as zonas DNS herdadas e deverá ser executada somente depois que você tiver verificado se a resolução DNS está funcionando conforme o esperado. Você será solicitado a excluir cada zona DNS privada. Insira ' Y ' em todos os prompts depois de verificar se a resolução DNS para essas zonas está funcionando corretamente.

![Limpeza](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Atualizar sua automação

Se você estiver usando a automação, incluindo modelos, scripts do PowerShell ou código personalizado desenvolvido usando o SDK, você deverá atualizar sua automação para usar o novo modelo de recurso para as zonas DNS privadas. Abaixo estão os links para a nova documentação de CLI/PS/SDK do DNS privado.
* [API REST de zonas privadas do DNS do Azure](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [CLI de zonas privadas do DNS do Azure](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [PowerShell de zonas privadas do DNS do Azure](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [SDK de zonas privadas do DNS do Azure](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Precisa de ajuda adicional

Crie um tíquete de suporte se precisar de ajuda adicional com o processo de migração ou por qualquer motivo que as etapas listadas acima não funcionem para você. Inclua o arquivo de transcrição gerado pelo script do PowerShell com seu tíquete de suporte.

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma zona privada no DNS do Azure usando [Azure PowerShell](./private-dns-getstarted-powershell.md) ou [CLI do Azure](./private-dns-getstarted-cli.md).

* Leia sobre alguns cenários comuns de [zona privada](./private-dns-scenarios.md) que podem ser percebidos com zonas privadas no DNS do Azure.

* Para perguntas e respostas comuns sobre zonas privadas no DNS do Azure, incluindo um comportamento específico que você pode esperar para determinados tipos de operações, consulte [DNS privado perguntas frequentes](./dns-faq-private.md).

* Saiba mais sobre zonas e registros DNS visitando [visão geral de zonas e registros DNS](dns-zones-records.md).

* Saiba mais sobre algumas das outras principais [capacidades de rede](../networking/networking-overview.md) do Azure.
