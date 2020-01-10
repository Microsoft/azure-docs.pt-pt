---
title: 'Gateway de VPN do Azure: gerar & exportar certificados para P2S: MakeCert'
description: Crie um certificado raiz autoassinado, exporte a chave pública e gere certificados de cliente usando o MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: ad2ab31e6771efc54238d5747863fa2a9bb2f356
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833978"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Gerar e exportar certificados para conexões ponto a site usando MakeCert

As conexões ponto a site usam certificados para autenticação. Este artigo mostra como criar um certificado raiz autoassinado e gerar certificados de cliente usando o MakeCert. Se você estiver procurando instruções de certificado diferentes, consulte [certificados-PowerShell](vpn-gateway-certificates-point-to-site.md) ou [certificados-Linux](vpn-gateway-certificates-point-to-site-linux.md).

Embora seja recomendável usar as [etapas do Windows 10 PowerShell](vpn-gateway-certificates-point-to-site.md) para criar seus certificados, fornecemos essas instruções MakeCert como um método opcional. Os certificados que você gera usando qualquer um dos métodos podem ser instalados em [qualquer sistema operacional cliente com suporte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). No entanto, o MakeCert tem a seguinte limitação:

* O MakeCert foi preterido. Isso significa que essa ferramenta pode ser removida em qualquer ponto. Todos os certificados que você já gerou usando o MakeCert não serão afetados quando o MakeCert não estiver mais disponível. O MakeCert é usado apenas para gerar os certificados, não como um mecanismo de validação.

## <a name="rootcert"></a>Criar um certificado raiz autoassinado

As etapas a seguir mostram como criar um certificado autoassinado usando o MakeCert. Essas etapas não são específicas do modelo de implantação. Eles são válidos para o Resource Manager e o clássico.

1. Baixe e instale o [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. Após a instalação, normalmente você pode encontrar o utilitário Makecert. exe neste caminho: ' C:\Program Files (x86) \Windows Kits\10\bin\<Arch > '. No entanto, é possível que ele tenha sido instalado em outro local. Abra um prompt de comando como administrador e navegue até o local do utilitário MakeCert. Você pode usar o seguinte exemplo, ajustando para o local apropriado:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Crie e instale um certificado no repositório de certificados pessoal no seu computador. O exemplo a seguir cria um arquivo *. cer* correspondente que você carrega no Azure ao configurar o P2S. Substitua ' P2SRootCert ' e ' P2SRootCert. cer ' pelo nome que você deseja usar para o certificado. O certificado está localizado em ' Certificates-Current User\Personal\Certificates '.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="cer"></a>Exportar a chave pública (. cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

O arquivo. cer exportado deve ser carregado no Azure. Para obter instruções, consulte [Configurar uma conexão ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Para adicionar um certificado raiz confiável adicional, consulte [esta seção](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) do artigo.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado autoassinado e a chave privada para armazená-lo (opcional)

Talvez você queira exportar o certificado raiz autoassinado e armazená-lo com segurança. Se necessário, você pode instalá-lo posteriormente em outro computador e gerar mais certificados de cliente ou exportar outro arquivo. cer. Para exportar o certificado raiz autoassinado como um. pfx, selecione o certificado raiz e use as mesmas etapas, conforme descrito em [exportar um certificado de cliente](#clientexport).

## <a name="create-and-install-client-certificates"></a>Criar e instalar certificados de cliente

Você não instala o certificado autoassinado diretamente no computador cliente. Você precisa gerar um certificado de cliente do certificado autoassinado. Em seguida, exporte e instale o certificado do cliente no computador cliente. As etapas a seguir não são específicas do modelo de implantação. Eles são válidos para o Resource Manager e o clássico.

### <a name="clientcert"></a>Gerar um certificado de cliente

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. Você gera um certificado de cliente do certificado raiz autoassinado e, em seguida, exporta e instala o certificado do cliente. Se o certificado do cliente não estiver instalado, a autenticação falhará. 

As etapas a seguir orientarão você na geração de um certificado de cliente de um certificado raiz autoassinado. Você pode gerar vários certificados de cliente do mesmo certificado raiz. Quando você gera certificados de cliente usando as etapas abaixo, o certificado do cliente é instalado automaticamente no computador que você usou para gerar o certificado. Se você quiser instalar um certificado de cliente em outro computador cliente, poderá exportar o certificado.
 
1. No mesmo computador que você usou para criar o certificado autoassinado, abra um prompt de comando como administrador.
2. Modifique e execute o exemplo para gerar um certificado de cliente.
   * Altere *"P2SRootCert"* para o nome da raiz autoassinada da qual você está gerando o certificado do cliente. Verifique se você está usando o nome do certificado raiz, que é qualquer que seja o valor ' CN = ' que você especificou quando criou a raiz autoassinada.
   * Altere *P2SChildCert* para o nome que você deseja gerar um certificado de cliente.

   Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado de cliente chamado P2SChildcert em seu repositório de certificados pessoal que foi gerado a partir do certificado raiz P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="clientexport"></a>Exportar um certificado de cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Instalar um certificado de cliente exportado

Para instalar um certificado de cliente, consulte [instalar um certificado de cliente](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Passos seguintes

Continue com a configuração ponto a site. 

* Para as etapas do modelo de implantação do **Resource Manager** , consulte [Configurar o P2S usando a autenticação de certificado nativa do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para obter as etapas do modelo de implantação **clássico** , consulte [Configurar uma conexão VPN ponto a site para uma VNet (clássica)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).