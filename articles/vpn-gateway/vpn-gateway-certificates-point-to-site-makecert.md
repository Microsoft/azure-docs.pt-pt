---
title: 'Azure VPN Gateway: Gerar & certificados de exportação para P2S: MakeCert'
description: Crie um certificado de raiz auto-assinado, exporte a chave pública e gere certificados de cliente usando a MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: ad2ab31e6771efc54238d5747863fa2a9bb2f356
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75833978"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Gerar e exportar certificados para ligações Ponto-a-Local utilizando o MakeCert

As ligações ponto-a-local utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz auto-assinado e gerar certificados de cliente usando o MakeCert. Se estiver à procura de instruções de certificado diferentes, consulte [Certificados - PowerShell](vpn-gateway-certificates-point-to-site.md) ou [Certificados - Linux](vpn-gateway-certificates-point-to-site-linux.md).

Embora recomendamos a utilização dos [passos PowerShell do Windows 10](vpn-gateway-certificates-point-to-site.md) para criar os seus certificados, fornecemos estas instruções MakeCert como um método opcional. Os certificados que gera utilizando qualquer método podem ser instalados em [qualquer sistema operativo de cliente suportado](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). No entanto, a MakeCert tem a seguinte limitação:

* MakeCert está depreceado. Isto significa que esta ferramenta pode ser removida em qualquer ponto. Quaisquer certificados que já tenha gerado usando o MakeCert não serão afetados quando a MakeCert já não estiver disponível. MakeCert é usado apenas para gerar os certificados, não como um mecanismo de validação.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Criar um certificado de raiz auto-assinado

Os seguintes passos mostram-lhe como criar um certificado auto-assinado usando o MakeCert. Estes passos não são específicos do modelo de implantação. São válidos tanto para o Gestor de Recursos como para o clássico.

1. Descarregue e instale [o MakeCert.](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx)
2. Após a instalação, você pode tipicamente encontrar o utilitário makecert.exe sob este caminho: 'C:\Program Files (x86)\Windows Kits\10\bin\<arch>'. Embora seja possível que tenha sido instalado noutro local. Abra um pedido de comando como administrador e navegue até à localização do utilitário MakeCert. Pode utilizar o seguinte exemplo, ajustando-se para a localização adequada:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Crie e instale um certificado na loja de certificados Pessoais no seu computador. O exemplo seguinte cria um ficheiro *.cer* correspondente que envia para O Azure ao configurar o P2S. Substitua 'P2SRootCert' e 'P2SRootCert.cer' com o nome que pretende utilizar para o certificado. O certificado está localizado nos seus 'Certificados - Utilizador Atual\Certificados Pessoais\'.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>Exportar a chave pública (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

O ficheiro exportado.cer deve ser enviado para O Azure. Para obter instruções, consulte [Configurar uma ligação Ponto-a-Local](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Para adicionar um certificado de raiz fidedigno adicional, consulte [esta secção](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) do artigo.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado auto-assinado e a chave privada para armazená-lo (opcional)

Pode querer exportar o certificado de raiz auto-assinado e armazená-lo com segurança. Se necessário, pode posteriormente instalá-lo noutro computador e gerar mais certificados de cliente, ou exportar outro ficheiro .cer. Para exportar o certificado de raiz auto-assinado como .pfx, selecione o certificado de raiz e utilize os mesmos passos descritos na [Exportação de um certificado](#clientexport)de cliente .

## <a name="create-and-install-client-certificates"></a>Criar e instalar certificados de cliente

Não instala o certificado auto-assinado diretamente no computador cliente. Precisa gerar um certificado de cliente a partir do certificado auto-assinado. Em seguida, exporta e instala o certificado de cliente para o computador cliente. Os seguintes passos não são específicos do modelo de implantação. São válidos tanto para o Gestor de Recursos como para o clássico.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Gerar um certificado de cliente

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. Gera um certificado de cliente a partir do certificado raiz auto-assinado e depois exporta e instala o certificado de cliente. Se o certificado de cliente não estiver instalado, a autenticação falha. 

Os seguintes passos passam por gerar um certificado de cliente a partir de um certificado de raiz auto-assinado. Pode gerar vários certificados de cliente a partir do mesmo certificado de raiz. Quando gera certificados de cliente utilizando os passos abaixo, o certificado de cliente é automaticamente instalado no computador que usou para gerar o certificado. Se quiser instalar um certificado de cliente noutro computador cliente, pode exportar o certificado.
 
1. No mesmo computador que usou para criar o certificado auto-assinado, abra um pedido de comando como administrador.
2. Modifique e execute a amostra para gerar um certificado de cliente.
   * Mude *"P2SRootCert"* para o nome da raiz auto-assinada de que está a gerar o certificado de cliente. Certifique-se de que está a usar o nome do certificado de raiz, que é qualquer que seja o valor 'CN=' que especificou quando criou a raiz auto-assinada.
   * Mude *o P2SChildCert* para o nome que pretende gerar um certificado de cliente para ser.

   Se executar o seguinte exemplo sem modificá-lo, o resultado é um certificado de cliente chamado P2SChildcert na sua loja de certificados Pessoais que foi gerado a partir do certificado raiz P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Exportar um certificado de cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalar um certificado de cliente exportado

Para instalar um certificado de cliente, consulte [Instalar um certificado de cliente.](point-to-site-how-to-vpn-client-install-azure-cert.md)

## <a name="next-steps"></a>Passos seguintes

Continue com a configuração Ponto-a-Local. 

* Para os passos do modelo de implementação **do Gestor de Recursos,** consulte [Configure P2S utilizando a autenticação de certificado azure nativo](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Para passos **clássicos** do modelo de implementação, consulte [Configure uma ligação VPN ponto-a-local a um VNet (clássico)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Para obter informações de resolução de problemas P2S, consulte [Resolução de problemas de ligações ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).