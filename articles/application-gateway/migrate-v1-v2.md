---
title: Migrar de v1 para v2 - Azure Application Gateway
description: Este artigo mostra-lhe como migrar Azure Application Gateway e Web Application Firewall de v1 para v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 653e432ca445451fc9da7155137052b9916d0d92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311602"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrar gateway de aplicações Azure e Firewall de aplicação web de v1 para v2

[A azure Application Gateway e Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) já estão disponíveis, oferecendo funcionalidades adicionais como autoscaling e redundância de zona de disponibilidade. No entanto, os gateways v1 existentes não são atualizados automaticamente para a v2. Se quiser migrar de V1 para V2, siga os passos deste artigo.

Há duas fases numa migração:

1. Migrar a configuração
2. Migrar o tráfego do cliente

Este artigo abrange a migração de configuração. A migração do tráfego de clientes varia consoante o seu ambiente específico. No entanto, [são fornecidas](#migrate-client-traffic)algumas recomendações gerais de alto nível.

## <a name="migration-overview"></a>Descrição geral da migração

Um script Azure PowerShell está disponível que faz o seguinte:

* Cria um novo Standard_v2 ou WAF_v2 gateway numa sub-rede de rede virtual que especifica.
* Copia perfeitamente a configuração associada à porta de entrada V1 Standard ou WAF para o recém-criado Standard_V2 ou WAF_V2 gateway.

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O novo portal V2 tem novos endereços IP públicos e privados. Não é possível mover os endereços IP associados ao gateway v1 existente perfeitamente para v2. No entanto, pode alocar um endereço IP público ou privado existente (não atribuído) ao novo gateway v2.
* Tem de fornecer um espaço de endereço IP para outra sub-rede dentro da sua rede virtual onde está localizado o seu gateway V1. O script não pode criar o gateway V2 em quaisquer subesí redes existentes que já tenham um gateway v1. No entanto, se a sub-rede existente já tiver um gateway v2, isso ainda pode funcionar desde que haja espaço suficiente para o endereço IP.
* Se tiver um grupo de segurança de rede ou rotas definidas pelo utilizador associadas à sub-rede de gateway v2, certifique-se de que cumprem os [requisitos de NSG](../application-gateway/configuration-infrastructure.md#network-security-groups) e [UDR](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes) para uma migração bem sucedida
* [As políticas de ponto final do serviço de rede virtual](../virtual-network/virtual-network-service-endpoint-policies-overview.md) não são atualmente suportadas numa sub-rede do Gateway de Aplicação.
* Para migrar uma configuração TLS/SSL, deve especificar todos os certificados TLS/SSL utilizados no seu gateway V1.
* Se tiver o modo FIPS ativado para o seu gateway V1, este não será migrado para o seu novo gateway V2. O modo FIPS não é suportado em V2.
* v2 não suporta IPv6, então IPv6 habilitado v1 gateways não são migrados. Se executar o guião, pode não estar completo.
* Se o gateway v1 tiver apenas um endereço IP privado, o script cria um endereço IP público e um endereço IP privado para o novo gateway v2. os gateways v2 atualmente não suportam apenas endereços IP privados.
* Os cabeçalhos com nomes que contenham qualquer outra coisa que não letras, dígitos, hífens e sublinhados não são passados para a sua aplicação. Isto aplica-se apenas aos nomes dos cabeçalhos, não aos valores dos cabeçalhos. Esta é uma mudança de rutura da V1.

## <a name="download-the-script"></a>Descarregue o script

Descarregue o roteiro de migração da [PowerShell Gallery.](https://www.powershellgallery.com/packages/AzureAppGWMigration)

## <a name="use-the-script"></a>Use o script

Existem duas opções para si, dependendo da configuração e preferências do ambiente PowerShell local:

* Se não tiver os módulos Azure Az instalados ou não se importar de desinstalar os módulos Azure Az, a melhor opção é utilizar a `Install-Script` opção para executar o script.
* Se precisar de manter os módulos Azure Az, a sua melhor aposta é descarregar o script e executá-lo diretamente.

Para determinar se tem os módulos Azure Az instalados, corra `Get-InstalledModule -Name az` . Se não vir nenhum módulo Az instalado, então pode usar o `Install-Script` método.

### <a name="install-using-the-install-script-method"></a>Instale usando o método Install-Script

Para utilizar esta opção, não deve ter os módulos Azure Az instalados no seu computador. Se estiverem instalados, o seguinte comando apresenta um erro. Pode desinstalar os módulos Azure Az, ou utilizar a outra opção para descarregar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando para obter a versão mais recente:

`Install-Script -Name AzureAppGWMigration -Force`

Este comando também instala os módulos Az necessários.  

### <a name="install-using-the-script-directly"></a>Instale usando o script diretamente

Se tiver alguns módulos Azure Az instalados e não os conseguir desinstalar (ou não quiser desinstalá-los), pode descarregar manualmente o script utilizando o separador **Manual descarregar** no link de descarregamento do script. O script é descarregado como um ficheiro nupkg cru. Para instalar o script a partir deste ficheiro nupkg, consulte [o Download manual do pacote](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Utilize `Connect-AzAccount` para ligar ao Azure.

1. Use `Import-Module Az` para importar os módulos Az.

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

   Parâmetros para o script:
   * **resourceId: [String]: Required** - Este é o ID de recursos Azure para o seu portal Standard v1 ou WAF v1 existente. Para encontrar este valor de cadeia, navegue até ao portal Azure, selecione o gateway de aplicação ou recurso WAF e clique no link **Propriedades** para o gateway. O ID de recursos está localizado nessa página.

     Também pode executar os seguintes comandos Azure PowerShell para obter o ID de recurso:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: Required** - Este é o espaço de endereço IP que alocou (ou quer atribuir) para uma nova sub-rede que contém o seu novo gateway V2. Isto deve ser especificado na notação CIDR. Por exemplo: 10.0.0.0/24. Não precisas de criar esta sub-rede com antecedência. O guião cria-o para ti se não existir.
   * **appgwName: [String]: Opcional**. Esta é uma cadeia que especifica para usar como nome para o novo Standard_v2 ou WAF_v2 gateway. Se este parâmetro não for fornecido, o nome do seu gateway v1 existente será usado com o sufixo *_v2* anexado.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Opcional**.  Uma lista separada por vírgula de objetos PSApplicationGatewaySslCertificate que cria para representar os certificados TLS/SSL do seu gateway v1 deve ser enviado para o novo gateway v2. Para cada um dos seus certificados TLS/SSL configurados para o seu gateway Standard v1 ou WAF v1, pode criar um novo objeto PSApplicationGatewaySslCertificate através do `New-AzApplicationGatewaySslCertificate` comando mostrado aqui. Precisa do caminho para o seu ficheiro TLS/SSL Cert e da palavra-passe.

     Este parâmetro só é opcional se não tiver ouvintes HTTPS configurados para o seu gateway v1 ou WAF. Se tiver pelo menos uma configuração do ouvinte HTTPS, tem de especificar este parâmetro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Pode passar `$mySslCert1, $mySslCert2` (separado por vírgula) no exemplo anterior como valores para este parâmetro no script.
   * **trustRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Opcional**. Uma lista separada por vírgula de objetos PSApplicationGatewayTrustedRootCertificate que cria para representar os [certificados Trusted Root](ssl-overview.md) para autenticação das suas instâncias de backend a partir do seu gateway v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Para criar uma lista de objetos PSApplicationGatewayTrustedRootCertificate, consulte [New-AzApplicationGatewayTrustedRootCertificate .](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)
   * **privateIpAddress: [String]: Opcional**. Um endereço IP privado específico que pretende associar ao seu novo gateway V2.  Isto deve ser do mesmo VNet que atribui para o seu novo portal V2. Se isto não for especificado, o script atribui um endereço IP privado para o seu gateway V2.
   * **publicIpResourceId: [String]: Opcional**. Os recursosidários existentes do endereço IP público (standard SKU) na sua subscrição que pretende atribuir ao novo gateway v2. Se isto não for especificado, o script atribui um novo IP público no mesmo grupo de recursos. O nome é o nome do gateway v2 com *-IP* anexado.
   * **validarIgação: [comutação]: Opcional**. Utilize este parâmetro se quiser que o script faça algumas validações básicas de comparação de configuração após a criação do gateway V2 e a cópia de configuração. Por padrão, não é feita nenhuma validação.
   * **enableAutoScale: [switch]: Opcional**. Utilize este parâmetro se quiser que o script ative a autoScaling no novo gateway v2 após a sua criação. Por predefinição, o AutoScaling é desativado. Pode sempre ative-lo manualmente mais tarde no recém-criado gateway V2.

1. Executar o script usando os parâmetros apropriados. Pode levar 5 a 7 minutos para terminar.

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

## <a name="migrate-client-traffic"></a>Migrar o tráfego de clientes

Primeiro, verifique duas vezes se o script criou com sucesso um novo gateway V2 com a configuração exata migrada do seu gateway v1. Pode verificar isto a partir do portal Azure.

Além disso, envie uma pequena quantidade de tráfego através do gateway V2 como um teste manual.
  
Aqui estão alguns cenários onde o seu atual gateway de aplicação (Standard) pode receber tráfego de clientes, e nossas recomendações para cada um:

* **Uma zona de DNS personalizada (por exemplo, contoso.com) que aponta para o endereço IP frontend (utilizando um registo A) associado ao seu portal Standard v1 ou WAF v1**.

    Pode atualizar o seu registo DNS para indicar a etiqueta IP ou DNS associada à sua Standard_v2 gateway de aplicações. Dependendo do TTL configurado no seu registo DNS, pode demorar algum tempo até que todo o tráfego do seu cliente migra para o seu novo gateway V2.
* **Uma zona de DNS personalizada (por exemplo, contoso.com) que aponta para a etiqueta DNS (por exemplo: *myappgw.eastus.cloudapp.azure.com* utilizando um registo CNAME) associada ao seu gateway v1**.

   Tem duas opções:

  * Se utilizar endereços IP públicos no seu gateway de aplicações, pode fazer uma migração granular controlada utilizando um perfil de Gestor de Tráfego para encaminhar gradualmente o tráfego (método de encaminhamento de tráfego ponderado) para o novo gateway v2.

    Pode fazê-lo adicionando as etiquetas DNS dos gateways de aplicações V1 e V2 ao [perfil de Gestor de Tráfego,](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)e CNAMEing seu registo de DNS personalizado (por exemplo, ) ao domínio De Gestor de Tráfego `www.contoso.com` (por exemplo, contoso.trafficmanager.net).
  * Ou, pode atualizar o seu registo DNS de domínio personalizado para apontar para a etiqueta DNS do novo gateway de aplicações V2. Dependendo do TTL configurado no seu registo DNS, pode demorar algum tempo até que todo o tráfego do seu cliente migra para o seu novo gateway V2.
* **Os seus clientes conectam-se ao endereço IP frontend do seu gateway de aplicações**.

   Atualize os seus clientes para utilizar o endereço IP(es) associado ao recém-criado gateway de aplicações V2. Recomendamos que não utilize endereços IP diretamente. Considere usar a etiqueta de nome DNS (por exemplo, yourgateway.eastus.cloudapp.azure.com) associada ao seu gateway de aplicações que pode CNAME para a sua própria zona de DNS personalizada (por exemplo, contoso.com).

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem limitações com o script Azure PowerShell para migrar a configuração de V1 para v2?

Sim. Ver [Ressalvas/Limitações.](#caveatslimitations)

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Este artigo e o script Azure PowerShell também são aplicáveis ao produto WAF do Gateway de Aplicação? 

Sim.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>O script Azure PowerShell também muda o tráfego da minha porta de entrada V1 para o recém-criado gateway V2?

N.º O script Azure PowerShell apenas migra a configuração. A migração real do tráfego é da sua responsabilidade e do seu controlo.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>O novo gateway V2 é criado pelo script Azure PowerShell de tamanho adequado para lidar com todo o tráfego que é atualmente servido pelo meu gateway v1?

O script Azure PowerShell cria um novo gateway V2 com um tamanho adequado para lidar com o tráfego no seu gateway v1 existente. A autoscalagem é desativada por predefinição, mas pode ativar a autoscalagem quando executar o script.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Configurei o meu portal V1 para enviar registos para o armazém do Azure. O script também replica esta configuração para v2?

N.º O script não replica esta configuração para v2. Deve adicionar a configuração do registo separadamente ao gateway v2 migrado.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Este script suporta certificados enviados para Azure KeyVault?

N.º Atualmente o script não suporta certificados em KeyVault. No entanto, isto está a ser considerado para uma versão futura.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Dei conta de alguns problemas com a utilização deste guião. Como posso conseguir ajuda?
  
Pode contactar o Suporte Azure sob o tema "Configuração e Configuração/Migração para V2 SKU". Saiba mais sobre [o suporte do Azure aqui.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o Gateway de Aplicações v2](application-gateway-autoscaling-zone-redundant.md)
