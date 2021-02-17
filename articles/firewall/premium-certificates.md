---
title: Certificados de pré-visualização Azure Firewall Premium
description: Para configurar corretamente a inspeção TLS na pré-visualização Azure Firewall Premium, tem de configurar e instalar certificados intermédios de CA.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3914a82903c293cf1a8306b5ecc1f542fef83e72
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549910"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Certificados de pré-visualização Azure Firewall Premium 

> [!IMPORTANT]
> O Azure Firewall Premium está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Para configurar corretamente a inspeção TLS de pré-visualização Azure Firewall Premium, deve fornecer um certificado ac intermédio válido e depositá-lo no cofre da Chave Azure.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Certificados utilizados por Azure Firewall Premium Preview

Existem três tipos de certificados utilizados numa implantação típica:

- **Certificado ac intermédio (Certificado CA)**

   A Certificate Authority (CA) é uma organização que é fidedigna para assinar certificados digitais. Uma AC verifica a identidade e a legitimidade de uma empresa ou indivíduo que solicite um certificado. Se a verificação for bem sucedida, a AC emite um certificado assinado. Quando o servidor apresenta o certificado ao cliente (por exemplo, o seu navegador web) durante um aperto de mão SSL/TLS, o cliente tenta verificar a assinatura contra uma lista de bons signatários *conhecidos.* Os navegadores da Web normalmente vêm com listas de CAs em que implicitamente confiam para identificar anfitriões. Se a autoridade não estiver na lista, como em alguns sites que assinam os seus próprios certificados, o navegador alerta o utilizador de que o certificado não é assinado por uma autoridade reconhecida e pergunta ao utilizador se pretende continuar as comunicações com o site não verificado.

- **Certificado de Servidor (certificado do Site)**

   Um certificado associado a nome de domínio específico. Se um website tiver um certificado válido, significa que uma autoridade de certificados tomou medidas para verificar se o endereço web realmente pertence a essa organização. Quando digita um URL ou segue um link para um website seguro, o seu navegador verifica o certificado para as seguintes características:
   - O endereço do site corresponde ao endereço no certificado.
   - O certificado é assinado por uma autoridade de certificado que o navegador reconhece como uma autoridade *de confiança.*
   
   Ocasionalmente, os utilizadores podem ligar-se a um servidor com um certificado não fidedquirou. O Azure Firewall deixará cair a ligação como se o servidor terminasse a ligação.

- **Certificado root CA (certificado de raiz)**

   Uma autoridade de certificados pode emitir vários certificados sob a forma de uma estrutura de árvores. Um certificado de raiz é o certificado mais alto da árvore.

A pré-visualização Azure Firewall Premium pode intercetar tráfego HTTP/S de saída e gerar automaticamente um certificado de servidor para `www.website.com` . Este certificado é gerado utilizando o certificado de AC intermédio que fornece. O navegador de utilizador final e as aplicações do cliente devem confiar no certificado Root CA da sua organização ou no certificado de CA intermédio para que este procedimento funcione. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Processo de certificado":::

## <a name="intermediate-ca-certificate-requirements"></a>Requisitos intermédios de certificados de CA

Certifique-se de que o seu certificado de CA está em conformidade com os seguintes requisitos:

- Quando implementado como um segredo do Cofre de Chaves, deve utilizar o PFX sem palavra-passe (Pkcs12) com um certificado e uma chave privada.

- Deve ser um único certificado, e não deve incluir toda a cadeia de certificados.  

- Deve ser válido por um ano adiantada.  

- Deve ser uma chave privada RSA com o tamanho mínimo de 4096 bytes.  

- Deve ter a `KeyUsage` extensão marcada como Crítica com a `KeyCertSign` bandeira (RFC 5280; 4.2.1.3 Utilização da chave).

- Deve ter a `BasicContraints` extensão marcada como Critical (RFC 5280; 4.2.1.9 Restrições Básicas).  

- A `CA` bandeira deve ser colocada para TRUE.

- O Comprimento do Caminho deve ser maior ou igual a um.

## <a name="azure-key-vault"></a>Azure Key Vault

[O Azure Key Vault](../key-vault/general/overview.md) é uma loja secreta gerida pela plataforma que pode usar para salvaguardar segredos, chaves e certificados TLS/SSL. O Azure Firewall Premium suporta a integração com o Key Vault para certificados de servidor que estão ligados a uma Política de Firewall.
 
Para configurar o seu cofre chave:

- Tens de importar um certificado existente com o seu par de chaves no teu cofre. 
- Em alternativa, também pode usar um segredo de cofre chave que é armazenado como um ficheiro PFX codificado sem palavra-passe, base-64 codificado.  Um ficheiro PFX é um certificado digital que contém chave privada e chave pública.
- Recomenda-se a utilização de um certificado de ca porque permite configurar um alerta com base na data de validade do certificado.
- Depois de importar um certificado ou um segredo, precisa definir políticas de acesso no cofre chave para permitir que a identidade tenha acesso ao certificado/segredo.
- O certificado de CA fornecido deve ser fidedigno pela sua carga de trabalho Azure. Certifique-se de que estão corretamente implantados.

Pode criar ou reutilizar uma identidade gerida atribuída pelo utilizador, que o Azure Firewall utiliza para obter certificados do Key Vault em seu nome. Para mais informações, veja [o que são identidades geridas para os recursos da Azure?](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>Configurar um certificado na sua apólice

Para configurar um certificado DEA na sua política Firewall Premium, selecione a sua política e, em seguida, selecione **a inspeção TLS (pré-visualização)**. Selecione **Ativado** na página de **inspeção TLS.** Em seguida, selecione o seu certificado de CA no Cofre da Chave Azure, como mostra o seguinte número:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Diagrama de visão geral do Azure Firewall Premium":::
 
> [!IMPORTANT]
> Para ver e configurar um certificado do portal Azure, tem de adicionar a sua conta de utilizador Azure à política de Acesso ao Cofre de Chaves. Forneça à sua conta de utilizador **Obter** e **Lista** em **Permissões Secretas**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Política de acesso a cofre de chaves Azure":::


## <a name="troubleshooting"></a>Resolução de problemas

Se o seu certificado de CA for válido, mas não puder aceder a FQDNs ou URLs sob inspeção TLS, verifique os seguintes itens:

- Certifique-se de que o certificado do servidor web é válido.  

- Certifique-se de que o certificado Root CA está instalado no sistema operativo do cliente.  

- Certifique-se de que o navegador ou cliente HTTPS contém um certificado de raiz válido. O Firefox e outros navegadores podem ter políticas especiais de certificação.  

- Certifique-se de que o tipo de destino URL na sua regra de aplicação cobre o caminho correto e quaisquer outras hiperligações incorporadas na página HTML de destino. Você pode usar wildcards para uma cobertura fácil de todo o caminho URL necessário.  


## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as funcionalidades Azure Firewall Premium](premium-features.md)
