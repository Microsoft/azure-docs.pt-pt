---
title: 'Gateway Azure VPN: Gerar certificados de exportação & para P2S: MakeCert'
description: Crie um certificado de raiz auto-assinado, exporte a chave pública e gere certificados de cliente usando o MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 926de9f3fd357cd9d9ca067e4f7beff7d03eec95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394196"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Gerar e exportar certificados para ligações ponto-a-local utilizando o MakeCert

As ligações ponto-a-local utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz auto-assinado e gerar certificados de cliente usando o MakeCert. Se procura diferentes instruções de certificado, consulte [Certificados - PowerShell](vpn-gateway-certificates-point-to-site.md) ou [Certificados - Linux](vpn-gateway-certificates-point-to-site-linux.md).

Embora recomendemos a utilização dos [passos PowerShell do Windows 10](vpn-gateway-certificates-point-to-site.md) para criar os seus certificados, fornecemos estas instruções MakeCert como um método opcional. Os certificados que gera utilizando qualquer método podem ser instalados em [qualquer sistema operativo de cliente suportado](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). No entanto, a MakeCert tem a seguinte limitação:

* MakeCert está depreciada. Isto significa que esta ferramenta pode ser removida a qualquer momento. Quaisquer certificados que já tenha gerado usando o MakeCert não serão afetados quando o MakeCert já não estiver disponível. O MakeCert é utilizado apenas para gerar os certificados, não como um mecanismo de validação.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Criar um certificado de raiz auto-assinado

Os passos seguintes mostram-lhe como criar um certificado auto-assinado utilizando o MakeCert. Estes passos não são específicos do modelo de implantação. São válidos tanto para o Gestor de Recursos como para o Clássico.

1. Faça o download e instale [o MakeCert.](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx)
2. Após a instalação, é normal que se encontre o utilitário makecert.exe neste caminho: 'C:\Program Files (x86)\Windows Kits\10\bin \<arch> '. Embora seja possível que tenha sido instalado noutro local. Abra um pedido de comando como administrador e navegue até à localização do utilitário MakeCert. Pode utilizar o seguinte exemplo, ajustando-se para a localização adequada:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Crie e instale um certificado na loja de certificados Pessoal no seu computador. O exemplo a seguir cria um ficheiro *.cer* correspondente que envia para Azure ao configurar o P2S. Substitua 'P2SRootCert' e 'P2SRootCert.cer' com o nome que pretende utilizar para o certificado. O certificado está localizado nos seus 'Certificados - Utilizador Atual\Personal\Certificates'.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Exportar a chave pública (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

O ficheiro exportado.cer deve ser enviado para Azure. Para obter instruções, consulte [configurar uma ligação ponto-a-local](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Para adicionar um certificado de raiz de confiança adicional, consulte [esta secção](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) do artigo.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado auto-assinado e a chave privada para armazená-lo (opcional)

Pode querer exportar o certificado de raiz auto-assinado e armazená-lo em segurança. Se necessário, pode instalá-lo mais tarde noutro computador e gerar mais certificados de cliente, ou exportar outro ficheiro .cer. Para exportar o certificado de raiz auto-assinado como .pfx, selecione o certificado raiz e utilize os mesmos passos descritos no [Certificado de Cliente Exportação](#clientexport).

## <a name="create-and-install-client-certificates"></a>Criar e instalar certificados de cliente

Não se instala o certificado auto-assinado diretamente no computador cliente. Precisa gerar um certificado de cliente a partir do certificado auto-assinado. Em seguida, exporta e instala o certificado de cliente para o computador cliente. Os seguintes passos não são específicos do modelo de implantação. São válidos tanto para o Gestor de Recursos como para o Clássico.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Gerar um certificado de cliente

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. Gera um certificado de cliente a partir do certificado de raiz auto-assinado e, em seguida, exporta e instala o certificado de cliente. Se o certificado do cliente não for instalado, a autenticação falha. 

Os passos seguintes acompanham-no através da geração de um certificado de cliente a partir de um certificado de raiz auto-assinado. Pode gerar vários certificados de cliente a partir do mesmo certificado de raiz. Quando gera certificados de cliente utilizando os passos abaixo, o certificado de cliente é automaticamente instalado no computador que usou para gerar o certificado. Se quiser instalar um certificado de cliente noutro computador cliente, pode exportar o certificado.
 
1. No mesmo computador que usou para criar o certificado auto-assinado, abra um pedido de comando como administrador.
2. Modifique e execute a amostra para gerar um certificado de cliente.
   * Altere *"P2SRootCert"* para o nome da raiz auto-assinada a partir da a que está a gerar o certificado de cliente. Certifique-se de que está a usar o nome do certificado raiz, que é qualquer que seja o valor 'CN=' que especificou quando criou a raiz auto-assinada.
   * Altere *o P2SChildCert* para o nome que pretende gerar um certificado de cliente para ser.

   Se executar o exemplo seguinte sem modificá-lo, o resultado é um certificado de cliente chamado P2SChildcert na sua loja de certificados Pessoal que foi gerado a partir do certificado de raiz P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Exportar um certificado de cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalar um certificado de cliente exportado

Para instalar um certificado de cliente, consulte [instalar um certificado de cliente.](point-to-site-how-to-vpn-client-install-azure-cert.md)

## <a name="next-steps"></a>Passos seguintes

Continue com a sua configuração Ponto-a-Local. 

* Para os passos do modelo de implementação **do Gestor de Recursos,** consulte [o Configure P2S utilizando a autenticação de certificados Azure nativo](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para passos de modelo de implementação **clássica,** consulte [configurar uma ligação VPN ponto-a-local a um VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).