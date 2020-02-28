---
title: Upgrade de Basic Internal para Standard Internal - Azure Load Balancer
description: Este artigo mostra-lhe como atualizar o Equilíbrio de Carga Interna Azure de Basic SKU para Standard SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: c2c909d8ef2be982d4dd4a70b5f35d03e8e71418
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659973"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Upgrade Azure Internal Load Balancer - Sem necessidade de ligação de saída
[O Azure Standard Load Balancer](load-balancer-overview.md) oferece um conjunto rico de funcionalidades e alta disponibilidade através de redundância de zona. Para saber mais sobre o Load Balancer SKU, consulte a [tabela de comparação](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Há duas fases numa atualização:

1. Migrar a configuração
2. Adicione VMs para apoiar piscinas de Balancer de Carga Padrão

Este artigo abrange a migração de configuração. Adicionar VMs a piscinas de backend pode variar dependendo do seu ambiente específico. No entanto, [são fornecidas](#add-vms-to-backend-pools-of-standard-load-balancer)algumas recomendações gerais de alto nível.

## <a name="upgrade-overview"></a>Descrição geral da atualização

Está disponível um script Azure PowerShell que faz o seguinte:

* Cria um Balancer de carga SKU Interno Padrão no local que especifica. Note que nenhuma [ligação](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) de saída não será fornecida pelo Balancer De carga Interna Padrão.
* Copia perfeitamente as configurações do Equilíbrio de Carga SKU Básico para o recém-criado Equilíbrio de Carga Standard.

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O script apenas suporta a atualização do Balancer de Carga Interna onde não é necessária nenhuma ligação de saída. Se necessitar de [ligação de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) para alguns dos seus VMs, consulte esta [página](upgrade-InternalBasic-To-PublicStandard.md) para obter instruções. 
* O Standard Load Balancer tem novos endereços públicos. É impossível mover os endereços IP associados ao equilíbrio de carga básico existente sem problemas para o Standard Load Balancer, uma vez que têm SKUs diferentes.
* Se o equilibrador de carga Standard for criado numa região diferente, não poderá associar os VMs existentes na região antiga ao recém-criado Standard Load Balancer. Para contornar esta limitação, certifique-se de criar um novo VM na nova região.
* Se o seu Balancer de Carga não tiver qualquer configuração IP frontal ou piscina de backend, é provável que acerte um erro ao executar o script. Por favor, certifique-se de que não estão vazios.

## <a name="download-the-script"></a>Descarregue o script

Descarregue o script de migração da [Galeria PowerShell.](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)
## <a name="use-the-script"></a>Use o script

Existem duas opções para si, dependendo da configuração e preferências locais do ambiente PowerShell:

* Se não tiver os módulos Azure Az instalados ou não se importar de desinstalar os módulos Azure Az, a melhor opção é utilizar a opção `Install-Script` para executar o script.
* Se precisa de manter os módulos Azure Az, a sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se tem os módulos Azure Az instalados, faça `Get-InstalledModule -Name az`. Se não vir nenhum módulo Az instalado, então pode utilizar o método `Install-Script`.

### <a name="install-using-the-install-script-method"></a>Instalar utilizando o método Instalação-Script

Para utilizar esta opção, não deve ter os módulos Azure Az instalados no seu computador. Se estiverem instalados, o comando seguinte apresenta um erro. Pode desinstalar os módulos Azure Az ou utilizar a outra opção para descarregar o script manualmente e executá-lo.
  
Executar o script com o seguinte comando:

`Install-Script -Name AzureILBUpgrade`

Este comando também instala os módulos Az necessários.  

### <a name="install-using-the-script-directly"></a>Instale diretamente o script

Se tiver alguns módulos Azure Az instalados e não os conseguir desinstalar (ou não os pretender desinstalar), pode descarregar manualmente o script utilizando o separador **Descarregamento Manual** no link de descarregamento do script. O script é descarregado como um ficheiro nupkg cru. Para instalar o script a partir deste ficheiro nupkg, consulte o [Manual Package Download](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Utilize `Connect-AzAccount` para ligar ao Azure.

1. Utilize `Import-Module Az` para importar os módulos Az.

1. Examinar os parâmetros necessários:

   * **rgName: [String]: Required** – Este é o grupo de recursos para o seu equilíbrio de carga básico existente e novo Equilíbrio de Carga Padrão. Para encontrar este valor de cadeia, navegue para o portal Azure, selecione a sua fonte de Equilíbrio de Carga Básica e clique na **visão geral** para o equilibrador de carga. O Grupo de Recursos está localizado nessa página.
   * **oldLBName: [String]: Required** – Este é o nome do seu Equilíbrio Básico existente que pretende atualizar. 
   * **newlocation: [String]: Required** – Este é o local em que o Balancer de Carga Padrão será criado. Recomenda-se herdar a mesma localização do Equilibrador básico de carga escolhido para o Equilíbrio de Carga Padrão para uma melhor associação com outros recursos existentes.
   * **newLBName: [String]: Required** – Este é o nome para o Balancer de Carga Padrão a ser criado.
1. Executar o script utilizando os parâmetros apropriados. Pode levar 5 a 7 minutos para terminar.

    **Exemplo**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Adicione VMs para apoiar piscinas de Balancer de Carga Padrão

Primeiro, verifique duas vezes se o script criou com sucesso um novo Balancer de Carga Interna Standard com a configuração exata migrada do seu Equilíbrio de Carga Interna Básica. Pode verificar isto a partir do portal Azure.

Certifique-se de enviar uma pequena quantidade de tráfego através do Balancer de Carga Padrão como um teste manual.
  
Aqui estão alguns cenários de como você adiciona VMs para backend piscinas do recém-criado Standard Internal Load Balancer pode ser configurado, e nossas recomendações para cada um:

* Movendo os **VMs existentes de piscinas de backend do antigo Equilibrante de Carga Interna Básica para piscinas de backend pools de recém-criado Standard Internal Load Balancer**.
    1. Para fazer as tarefas neste arranque rápido, inicie sessão no [portal Azure](https://portal.azure.com).
 
    1. **Selecione todos os recursos** no menu esquerdo e, em seguida, selecione o **recém-criado Standard Load Balancer** da lista de recursos.
   
    1. Em **Definições**, selecione **Conjuntos de back-end**.
   
    1. Selecione a piscina de backend que corresponde ao pool de backend do Equilíbrio de Carga Básica, selecione o seguinte valor: 
      - **Máquina Virtual**: Desça e selecione os VMs da piscina de backend correspondente do Equilíbrio de Carga Básica.
    1. Selecione **Guardar**.
    >[!NOTE]
    >Para vMs que têm IPs públicos, você precisará criar endereços IP padrão primeiro onde o mesmo endereço IP não está garantido. Desassocie os VMs dos IPs básicos e associe-os aos endereços IP padrão recém-criados. Em seguida, poderá seguir as instruções para adicionar VMs no backend pool do Standard Load Balancer. 

* **Criar novos VMs para adicionar às piscinas de backend do recém-criado Standard Internal Load Balancer**.
    * Mais instruções sobre como criar VM e associá-lo ao Standard Load Balancer pode ser encontrado [aqui](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem limitações com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Ver [Ressalvas/Limitações](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script Azure PowerShell também muda o tráfego do meu Equilíbrio de Carga Básico para o recém-criado Standard Load Balancer?

Não. O script Azure PowerShell apenas migra a configuração. A migração de tráfego real é da sua responsabilidade e do seu controlo.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Tive alguns problemas com o uso deste guião. Como posso conseguir ajuda?
  
Pode enviar um e-mail para slbupgradesupport@microsoft.com, abrir um caso de suporte com o Suporte Azure, ou fazer ambos.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o Balancer de Carga Padrão](load-balancer-overview.md)
