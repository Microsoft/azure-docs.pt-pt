---
title: Migrar o Gateway de aplicação do Azure e de Firewall de aplicações do v1 para v2 Web
description: Este artigo mostra como migrar o Gateway de aplicação do Azure e de Firewall de aplicações Web do v1 para v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: f55beca65a19ee9e47708000976dd42a6f252e2e
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154158"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrar o Gateway de aplicação do Azure e de Firewall de aplicações do v1 para v2 Web

[Gateway de aplicação do Azure e de Firewall de aplicações Web (WAF) v2](application-gateway-autoscaling-zone-redundant.md) estão agora disponíveis, oferecendo recursos adicionais, tais como o dimensionamento automático e zona de disponibilidade de redundância. No entanto, os gateways v1 existentes não são atualizados automaticamente para v2. Se pretender migrar do v1 para v2, siga os passos neste artigo.

Existem duas fases numa migração:

1. Migrar a configuração
2. Migrar o tráfego de cliente

Este artigo abrange a migração de configuração. Migração de tráfego do cliente varia consoante o seu ambiente específico. No entanto, algumas recomendações de alto nível, gerais [são fornecidos](#migrate-client-traffic).

## <a name="migration-overview"></a>Descrição geral da migração

Um script do PowerShell do Azure está disponível que faz o seguinte:

* Cria um novo gateway Standard_v2 ou WAF_v2 numa sub-rede de rede virtual que especificou.
* Forma totalmente integrada copia a configuração associada ao gateway Standard ou WAF v1 para o gateway de Standard_V2 ou WAF_V2 recém-criado.

### <a name="caveatslimitations"></a>Caveats\Limitations

* O novo gateway v2 tem novos endereços IP públicos e privados. Não é possível mover os endereços IP associados com o gateway existente do v1 forma totalmente integrada para v2. No entanto, pode alocar um existente (não alocado) público ou privado endereço IP para o novo gateway v2.
* Tem de fornecer um espaço de endereços IP para outra sub-rede dentro da sua rede virtual onde se encontra o seu gateway v1. O script não é possível criar o gateway da v2 em quaisquer sub-redes existentes que já tem um gateway de v1. No entanto, se a sub-rede existente já tiver um gateway de v2, que ainda pode funcionar desde que haja suficiente espaço de endereços IP.
* Para migrar uma configuração de SSL, tem de especificar todos os certificados SSL utilizados no gateway de v1.
* Se tiver de modo FIPS ativado para o seu gateway V1, não serão migrado para o novo gateway de v2. Modo FIPS não é suportado no v2.
* v2 não suporta IPv6, para que os gateways de v1 do IPv6 ativado não são migrados. Se executar o script, ele pode não ser concluído.
* Se o gateway de v1 tem apenas um endereço IP privado, o script cria um endereço IP público e um endereço IP privado para o novo gateway v2. gateways de v2 atualmente não é suportada apenas endereços IP privados.

## <a name="download-the-script"></a>Transferir o script

Transferir o script de migração a partir da [galeria do PowerShell](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Utilize o script

Existem duas opções para, dependendo de sua configuração de ambiente de PowerShell local e as preferências:

* Se não tem os módulos do Azure Az instalados ou não me importo desinstalar os módulos do Azure Az, a melhor opção é usar o `Install-Script` opção para executar o script.
* Se precisar de manter os módulos do Azure Az, sua melhor aposta é transferir o script e executá-lo diretamente.

Para determinar se tem os módulos do Azure Az instalados, execute `Get-InstalledModule -Name az`. Se não vir quaisquer módulos de Az de instalados, em seguida, pode utilizar o `Install-Script` método.

### <a name="install-using-the-install-script-method"></a>Instalar usando o método de Script de instalação

Para utilizar esta opção, não tem de ter os módulos do Azure Az instalados no seu computador. Se estiver instalados, o comando seguinte apresenta um erro. Pode desinstalar os módulos do Azure Az ou utilizar outra opção para transferir o script manualmente e executá-lo.
  
Execute o script com o seguinte comando:

`Install-Script -Name AzureAppGWMigration`

Este comando também instala os módulos de Az necessários.  

### <a name="install-using-the-script-directly"></a>Instalar utilizando o script diretamente

Se tiver alguns módulos do Azure Az instalados e não pode desinstalá-las (ou não pretenda desinstalá-las), pode transferir manualmente o script com o **transferir o Manual** separador o link de download do script. O script é transferido como um ficheiro de nupkg não processados. Para instalar o script a partir desse arquivo nupkg, consulte [transferir o pacote Manual](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Utilize `Connect-AzAccount` para ligar ao Azure.

1. Utilize `Import-Module Az` para importar os módulos de Az.

1. Executar `Get-Help AzureAppGWMigration.ps1` para examinar os parâmetros necessários:

   `AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale`

   Parâmetros do script:
   * **resourceId: [String]: Necessário** -este é o ID de recurso do Azure para o seu v1 padrão existentes ou a WAF do gateway de v1. Para localizar este valor de cadeia de caracteres, navegue para o portal do Azure, selecione o seu gateway de aplicação ou recurso de WAF e clique nas **propriedades** ligação para o gateway. O ID de recurso está localizado nessa página.

     Também pode executar os seguintes comandos do Azure PowerShell para obter o ID de recurso:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]:  Necessário** -este é o espaço de endereços IP que alocou (ou pretende alocar) para uma nova sub-rede que contém o novo gateway de v2. Isso deve ser especificado na notação CIDR. Por exemplo: 10.0.0.0/24. Não precisa de criar esta sub-rede com antecedência. O script cria-a para se não existir.
   * **appgwName: [String]: Opcional**. Esta é uma cadeia de caracteres que especifica para utilizar como o nome para o novo gateway Standard_v2 ou WAF_v2. Se este parâmetro não for fornecido, será utilizado o nome do seu gateway v1 existente com o sufixo *_v2* anexado.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Opcional**.  Uma lista separada por vírgulas de objetos de PSApplicationGatewaySslCertificate que criar para representar os certificados SSL do seu gateway v1 tem de ser carregada para o novo gateway v2. Para cada um dos seus certificados SSL configurados para a v1 padrão ou a WAF do gateway de v1, pode criar um novo objeto de PSApplicationGatewaySslCertificate via o `New-AzApplicationGatewaySslCertificate` comando mostrado aqui. Terá do caminho para o ficheiro de certificado SSL e a palavra-passe.

       Este parâmetro só é opcional, se não tiver serviços de escuta HTTPS configurados para o seu gateway de v1 ou WAF. Se tiver, pelo menos, um programa de configuração de serviço de escuta HTTPS, tem de especificar este parâmetro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      Pode passar `$mySslCert1, $mySslCert2` (separados por vírgulas) no exemplo anterior como valores para este parâmetro no script.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Opcional**. Uma lista separada por vírgulas de objetos de PSApplicationGatewayTrustedRootCertificate que criar para representar a [certificados de raiz fidedigna](ssl-overview.md) para a autenticação das suas instâncias de back-end do seu gateway v2.  

      Para criar uma lista de objetos de PSApplicationGatewayTrustedRootCertificate, veja [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: Opcional**. Um endereço IP privado específico que pretende associar ao novo gateway v2.  Tem de ser da mesma VNet que alocar para o novo gateway de v2. Se isto não for especificado, o script aloca um endereço IP privado para o seu gateway v2.
    * **publicIpResourceId: [String]: Opcional**. O resourceId de um recurso de endereço (standard SKU) de IP público na sua subscrição que pretende alocar para o novo gateway v2. Se isto não for especificado, o script aloca um novo IP público no mesmo grupo de recursos. O nome é o nome do gateway v2 com *- IP* anexado.
   * **validateMigration: [switch]: Opcional**. Utilize este parâmetro se pretender que o script para fazer alguma configuração básica validações de comparação após a criação de gateway v2 e a cópia de configuração. Por predefinição, é efetuada nenhuma validação.
   * **enableAutoScale: [mudar]: Opcional**. Utilize este parâmetro se pretender que o script para ativar o dimensionamento automático no novo gateway v2 depois de criado. Por predefinição, o dimensionamento automático está desativado. Pode sempre ativá-lo manualmente mais tarde o gateway recentemente criado v2.

1. Execute o script com os parâmetros adequados. Poderá demorar cinco a sete minutos a concluir.

    **Exemplo**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrar o tráfego de cliente

Em primeiro lugar, volte a verificar que o script criado com êxito um novo gateway v2 com a configuração exata migrados ao longo do seu gateway v1. Pode verificar isto no portal do Azure.

Além disso, envie uma pequena quantidade de tráfego através do gateway de v2 como um teste manual.
  
Seguem-se alguns cenários em que o gateway de aplicação atual (Standard) pode receber o tráfego de cliente e as nossas recomendações para cada um deles:

* **Uma zona DNS (por exemplo, contoso.com) personalizada que aponta para o endereço IP de front-end (através de um registo) associado à sua v1 padrão ou WAF do gateway de v1**.

    Pode atualizar o registo DNS para apontar para a etiqueta IP ou DNS de front-end associada com o gateway de aplicação Standard_v2. Dependendo do valor de TTL configurado no seu registo de DNS, poderá demorar algum tempo para todo o tráfego de cliente migrar para o novo gateway de v2.
* **Uma zona DNS (por exemplo, contoso.com) personalizada que aponta para a etiqueta DNS (por exemplo: *myappgw.eastus.cloudapp.azure.com* com um registo CNAME) associado ao seu gateway v1**.

   Há duas opções:

  * Se utilizar endereços IP públicos no seu gateway de aplicação, pode fazer controlado, migração granular através de um perfil de Gestor de tráfego para incrementalmente encaminhar o tráfego (método de encaminhamento de tráfego ponderado) para o novo gateway v2.

    Pode fazê-lo ao adicionar as etiquetas de DNS de ambos o v1 e v2 gateways de aplicação para o [perfil do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)e Vou criar o seu registo DNS personalizado (por exemplo, www.contoso.com) para o domínio do Gestor de tráfego (por exemplo, contoso.trafficmanager.NET).
  * Em alternativa, pode atualizar o registo DNS de domínio personalizado para apontar para a etiqueta DNS do novo gateway de aplicação v2. Dependendo do valor de TTL configurado no seu registo de DNS, poderá demorar algum tempo para todo o tráfego de cliente migrar para o novo gateway de v2.
* **Os clientes estabelecem a ligação do front-end endereço IP do seu gateway de aplicação**.

   Atualize seus clientes para utilizar os endereços IP associado ao gateway de aplicação v2 recém-criado. Recomendamos que não utilize endereços IP diretamente. Considere utilizar a etiqueta de nome DNS (por exemplo, yourgateway.eastus.cloudapp.azure.com) associados com o gateway de aplicação que pode CNAME para sua própria zona DNS personalizada (por exemplo, contoso.com).

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem limitações com o script do Azure PowerShell para migrar a configuração do v1 para v2?

Sim. Ver [advertências/limitações](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Se este artigo e o script do PowerShell do Azure aplica-se para o produto de WAF do Gateway de aplicação também? 

Sim.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>O script do PowerShell do Azure também alternar sobre o tráfego do meu gateway de v1 para o gateway recentemente criado v2?

Não. O script do PowerShell do Azure só migra a configuração. Migração de tráfego real é da responsabilidade do cliente e em seu controle.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>É o novo gateway v2 criado pelo script do Azure PowerShell o tamanho adequado para lidar com todo o tráfego que atualmente é servido pelo meu gateway v1?

O script do PowerShell do Azure cria um novo gateway v2 com um tamanho adequado para processar o tráfego no seu gateway v1 existente. Dimensionamento automático está desativado por predefinição, mas pode ativar o dimensionamento automático ao executar o script.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Configurei meu gateway v1 para enviar registos para o armazenamento do Azure. O script é replicado esta configuração para a v2 também?

Não. O script não replicar esta configuração para v2. Tem de adicionar a configuração de registo em separado para o gateway de v2 migrados.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Que eu encontrei alguns problemas com o uso deste script. Como posso obter ajuda?
  
Pode enviar um e-mail para appgwmigrationsup@microsoft.com, abrir um incidente de suporte com o suporte do Azure ou fazer as duas coisas.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre o Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
