---
title: Migrar de v1 para v2 - Gateway de aplicação Azure
description: Este artigo mostra-lhe como migrar o Portal de Aplicações Azure e firewall de aplicação web de v1 para v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 96f3825288846e86771ef3907eb4da4e58630df3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475186"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Portal de aplicações Migrate Azure e Firewall de aplicação web de v1 para v2

[O Azure Application Gateway e o Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) já estão disponíveis, oferecendo funcionalidades adicionais, como autoscalcificação e redundância na zona de disponibilidade. No entanto, os gateways v1 existentes não são atualizados automaticamente para a v2. Se quiser migrar da v1 para a v2, siga os passos deste artigo.

Há duas fases numa migração:

1. Migrar a configuração
2. Migrar o tráfego do cliente

Este artigo abrange a migração de configuração. A migração do tráfego do cliente varia consoante o seu ambiente específico. No entanto, [são fornecidas](#migrate-client-traffic)algumas recomendações gerais de alto nível.

## <a name="migration-overview"></a>Descrição geral da migração

Está disponível um script Azure PowerShell que faz o seguinte:

* Cria um novo portal de Standard_v2 ou WAF_v2 numa subnet de rede virtual que especifica.
* Copia perfeitamente a configuração associada à porta de entrada v1 Standard ou WAF para o recém-criado portal Standard_V2 ou WAF_V2.

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O novo gateway v2 tem novos endereços IP públicos e privados. Não é possível mover os endereços IP associados ao gateway v1 existente perfeitamente para v2. No entanto, pode atribuir um endereço IP público ou privado (não atribuído) existente ao novo gateway v2.
* Deve fornecer um espaço de endereço IP para outra subrede dentro da sua rede virtual onde se encontra o gateway V1. O script não pode criar o gateway v2 em quaisquer subredes existentes que já tenham um gateway v1. No entanto, se a subnet existente já tiver um gateway v2, isso ainda pode funcionar desde que haja espaço suficiente para endereços IP.
* Para migrar uma configuração SSL, deve especificar todas as certas SSL utilizadas no seu gateway V1.
* Se tiver o modo FIPS ativado para o seu gateway V1, não será migrado para o seu novo gateway V2. O modo FIPS não é suportado em v2.
* v2 não suporta iPv6, por isso os gateways v1 habilitados iPv6 não são migrados. Se executar o guião, pode não estar completo.
* Se o gateway v1 tiver apenas um endereço IP privado, o script cria um endereço IP público e um endereço IP privado para o novo gateway v2. os gateways v2 atualmente não suportam apenas endereços IP privados.
* Os cabeçalhos com nomes que contenham outras letras, dígitos, hífens e sublinhados não são passados para a sua aplicação. Isto aplica-se apenas a nomes de cabeçalho, não a valores de cabeçalho. Esta é uma mudança de rutura da V1.

## <a name="download-the-script"></a>Descarregue o script

Descarregue o script de migração da [Galeria PowerShell.](https://www.powershellgallery.com/packages/AzureAppGWMigration)

## <a name="use-the-script"></a>Use o script

Existem duas opções para si, dependendo da configuração e preferências locais do ambiente PowerShell:

* Se não tiver os módulos Azure Az instalados ou não se importar de desinstalar os módulos `Install-Script` Azure Az, a melhor opção é usar a opção de executar o script.
* Se precisa de manter os módulos Azure Az, a sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se tem os módulos Azure `Get-InstalledModule -Name az`Az instalados, corra . Se não vir nenhum módulo Az instalado, então `Install-Script` pode utilizar o método.

### <a name="install-using-the-install-script-method"></a>Instalar utilizando o método Instalação-Script

Para utilizar esta opção, não deve ter os módulos Azure Az instalados no seu computador. Se estiverem instalados, o comando seguinte apresenta um erro. Pode desinstalar os módulos Azure Az ou utilizar a outra opção para descarregar o script manualmente e executá-lo.
  
Executar o script com o seguinte comando:

`Install-Script -Name AzureAppGWMigration`

Este comando também instala os módulos Az necessários.  

### <a name="install-using-the-script-directly"></a>Instale diretamente o script

Se tiver alguns módulos Azure Az instalados e não os conseguir desinstalar (ou não os pretender desinstalar), pode descarregar manualmente o script utilizando o separador **Descarregamento Manual** no link de descarregamento do script. O script é descarregado como um ficheiro nupkg cru. Para instalar o script a partir deste ficheiro nupkg, consulte o [Manual Package Download](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Utilize `Connect-AzAccount` para ligar ao Azure.

1. Utilize `Import-Module Az` para importar os módulos Az.

1. Corra `Get-Help AzureAppGWMigration.ps1` para examinar os parâmetros necessários:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parâmetros para o guião:
   * **recursosId: [String]: Required** - Este é o ID de recurso Azure para o seu gateway V1 ou WAF v1 existente. Para encontrar este valor de cadeia, navegue para o portal Azure, selecione o seu portal de aplicação ou recurso WAF e clique no link **Propriedades** para o gateway. O ID de recurso está localizado nessa página.

     Também pode executar os seguintes comandos Azure PowerShell para obter o ID de recursos:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: Required** - Este é o espaço de endereço IP que atribuiu (ou quer alocar) para uma nova subnet que contenha o seu novo gateway v2. Isto deve ser especificado na notação CIDR. Por exemplo: 10.0.0.0/24. Não precisas de criar esta sub-rede com antecedência. O guião cria-o para ti se não existir.
   * **appgwName: [String]: Opcional**. Esta é uma cadeia que especifica para usar como nome para o novo portal de Standard_v2 ou WAF_v2. Se este parâmetro não for fornecido, o nome do seu gateway v1 existente será utilizado com o sufixo *_v2* anexado.
   * **sslCertificados: [PSApplicationGatewaySslCertificado]: Opcional**.  Uma lista separada da vírposta de objetos PSApplicationGatewaySslCertificate que cria para representar as certs SSL do seu gateway v1 deve ser enviada para o novo gateway v2. Para cada um dos seus certificados SSL configurados para o seu gateway Standard v1 ou WAF v1, pode criar um novo objeto PSApplicationGatewaySslCertificate através do `New-AzApplicationGatewaySslCertificate` comando aqui mostrado. Precisa do caminho para o seu ficheiro SSL Cert e a palavra-passe.

     Este parâmetro só é opcional se não tiver ouvintes HTTPS configurados para o seu gateway v1 ou WAF. Se tiver pelo menos uma configuração de escuta HTTPS, deve especificar este parâmetro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Pode passar `$mySslCert1, $mySslCert2` (separado sem vírem) no exemplo anterior como valores para este parâmetro no script.
   * **trustRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Opcional**. Uma lista separada da vírposta de objetos PSApplicationGatewayTrustedRootCertificate que cria para representar os [certificados De Raiz Fidedigno](ssl-overview.md) para autenticação das instâncias de backend do seu gateway v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Para criar uma lista de objetos PSApplicationGatewayTrustRootCertificate, consulte [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Opcional**. Um endereço IP privado específico que pretende associar ao seu novo gateway v2.  Isto deve ser do mesmo VNet que você atribui para o seu novo gateway V2. Se isto não for especificado, o script atribui um endereço IP privado para o seu gateway V2.
   * **publicIpResourceId: [String]: Opcional**. O recurso Id do recurso IP público existente (SKU padrão) na sua subscrição que pretende atribuir ao novo gateway v2. Se isto não for especificado, o guião atribui um novo IP público no mesmo grupo de recursos. O nome é o nome do gateway v2 com *-IP* anexado.
   * **validaMigração: [switch]: Opcional**. Utilize este parâmetro se quiser que o script faça algumas validações básicas de comparação de configuração após a criação do gateway v2 e a cópia de configuração. Por defeito, não é feita validação.
   * **enableAutoScale: [switch]: Opcional**. Utilize este parâmetro se quiser que o script ative a autoscalcificação no novo gateway v2 após a sua criação. Por predefinição, a autoscalcificação é desativada. Pode sempre ativar manualmente mais tarde o portal V2 recém-criado.

1. Executar o script utilizando os parâmetros apropriados. Pode levar 5 a 7 minutos para terminar.

    **Exemplo**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrar tráfego de clientes

Primeiro, verifique duas vezes se o script criou com sucesso um novo gateway v2 com a configuração exata migrada do seu gateway v1. Pode verificar isto a partir do portal Azure.

Além disso, envie uma pequena quantidade de tráfego através do gateway v2 como um teste manual.
  
Aqui estão alguns cenários em que o seu portal de aplicação atual (Standard) pode receber tráfego de clientes, e nossas recomendações para cada um:

* **Uma zona DNS personalizada (por exemplo, contoso.com) que aponta para o endereço IP frontal (utilizando um disco A) associado ao seu gateway Standard v1 ou WAF v1**.

    Pode atualizar o seu registo DNS para apontar para a etiqueta IP ou DNS frontend associada ao seu gateway de aplicação Standard_v2. Dependendo do TTL configurado no seu registo DNS, pode demorar algum tempo para que todo o tráfego do seu cliente migrasse para o seu novo gateway V2.
* **Uma zona DNS personalizada (por exemplo, contoso.com) que aponta para a etiqueta DNS (por exemplo: *myappgw.eastus.cloudapp.azure.com* utilizando um disco CNAME) associada ao seu gateway v1**.

   Tem duas opções:

  * Se utilizar endereços IP públicos no seu gateway de aplicação, pode fazer uma migração granular controlada utilizando um perfil do Traffic Manager para encaminhar gradualmente o tráfego (método de encaminhamento de tráfego ponderado) para o novo gateway v2.

    Pode fazê-lo adicionando as etiquetas DNS dos gateways de aplicação v1 e v2 ao [perfil do](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method) `www.contoso.com`Gestor de Tráfego , e cnameing o seu registo DNS personalizado (por exemplo), ao domínio do Gestor de Tráfego (por exemplo, contoso.trafficmanager.net).
  * Ou, pode atualizar o seu registo dNS de domínio personalizado para apontar para a etiqueta DNS do novo gateway de aplicação v2. Dependendo do TTL configurado no seu registo DNS, pode demorar algum tempo para que todo o tráfego do seu cliente migrasse para o seu novo gateway V2.
* **Os seus clientes ligam-se ao endereço IP frontend do seu portal de aplicações.**

   Atualize os seus clientes para utilizar o endereço IP(es) associado ao recém-criado gateway de aplicações V2. Recomendamos que não utilize endereços IP diretamente. Considere utilizar a etiqueta de nome DNS (por exemplo, yourgateway.eastus.cloudapp.azure.com) associada ao seu portal de aplicação que pode CNAME para a sua própria zona DNS personalizada (por exemplo, contoso.com).

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem limitações com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Ver [Ressalvas/Limitações](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Este artigo e o script Azure PowerShell também são aplicáveis ao produto WAF de Aplicação Gateway? 

Sim.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>O script Azure PowerShell também muda o tráfego da minha porta de entrada V1 para o recém-criado gateway V2?

Não. O script Azure PowerShell apenas migra a configuração. A migração de tráfego real é da sua responsabilidade e do seu controlo.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>O novo gateway v2 criado pelo script Azure PowerShell é adequado para lidar com todo o tráfego que é atualmente servido pelo meu portal v1?

O script Azure PowerShell cria um novo gateway v2 com um tamanho adequado para lidar com o tráfego no seu gateway v1 existente. A autodimensionamento é desativada por padrão, mas pode ativar a autoscalcificação quando executa o script.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Configurei a minha porta de entrada v1 para enviar registos para o armazém do Azure. O guião também replica esta configuração para v2?

Não. O guião não replica esta configuração para v2. Deve adicionar a configuração de registo separadamente ao gateway v2 migrado.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Este script suporta certificados enviados para Azure KeyVault?

Não. Atualmente, o script não suporta certificados no KeyVault. No entanto, isto está a ser considerado para uma versão futura.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Tive alguns problemas com o uso deste guião. Como posso conseguir ajuda?
  
Pode enviar um appgwmigrationsup@microsoft.come-mail para, abrir um caso de suporte com o Suporte Azure, ou fazer os dois.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o Gateway de Aplicação v2](application-gateway-autoscaling-zone-redundant.md)
