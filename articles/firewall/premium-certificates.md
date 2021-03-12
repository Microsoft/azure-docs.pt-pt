---
title: Certificados de pré-visualização Azure Firewall Premium
description: Para configurar corretamente a inspeção TLS na pré-visualização Azure Firewall Premium, tem de configurar e instalar certificados intermédios de CA.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: 47ebc752dedd72bbdedc02908911f1686584acda
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615504"
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


## <a name="create-your-own-self-signed-ca-certificate"></a>Crie o seu próprio certificado de CA auto-assinado

Para ajudá-lo a testar e verificar a inspeção TLS, pode utilizar os seguintes scripts para criar o seu próprio Ca Raiz e CA Intermédio auto-assinado.

> [!IMPORTANT]
> Para produção, deve utilizar o seu PKI corporativo para criar um certificado de CA intermédio. Um PKI corporativo aproveita a infraestrutura existente e trata da distribuição root CA para todas as máquinas de ponto final. Para obter mais informações, consulte [implementar e configurar os certificados da Enterprise CA para pré-visualização do Azure Firewall](premium-deploy-certificates-enterprise-ca.md).

Existem duas versões deste guião:
- um roteiro de bash `cert.sh` 
- um script PowerShell `cert.ps1` 

 Além disso, ambos os scripts usam o `openssl.cnf` ficheiro de configuração. Para utilizar os scripts, copie o conteúdo de `openssl.cnf` , e ou para o seu computador `cert.sh` `cert.ps1` local.

Os scripts geram os seguintes ficheiros:
- rootCA.crt/rootCA.key - Certificado público Root CA e chave privada.
- interCA.crt/interCA.key - Certificado público intermediário ca e chave privada
- interCA.pfx - Pacote intermediário ca pkcs12 que será usado por firewall

> [!IMPORTANT]
> rootCA.key deve ser armazenado num local offline seguro. Os scripts geram um certificado com validade de 1024 dias.
> Os scripts requerem binários de abertura instalados na sua máquina local. Para mais informações consulte https://www.openssl.org/
> 
Após a criação dos certificados, desloque-os para os seguintes locais:
- rootCA.crt - Implementar em máquinas de ponto final (apenas certificado público).
- interCA.pfx - Importe como certificado num Cofre-Chave e atribua à política de firewall.

### <a name="opensslcnf"></a>**openssl.cnf**
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Roteiro de bash - cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell - cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="troubleshooting"></a>Resolução de problemas

Se o seu certificado de CA for válido, mas não puder aceder a FQDNs ou URLs sob inspeção TLS, verifique os seguintes itens:

- Certifique-se de que o certificado do servidor web é válido.  

- Certifique-se de que o certificado Root CA está instalado no sistema operativo do cliente.  

- Certifique-se de que o navegador ou cliente HTTPS contém um certificado de raiz válido. O Firefox e outros navegadores podem ter políticas especiais de certificação.  

- Certifique-se de que o tipo de destino URL na sua regra de aplicação cobre o caminho correto e quaisquer outras hiperligações incorporadas na página HTML de destino. Você pode usar wildcards para uma cobertura fácil de todo o caminho URL necessário.  


## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as funcionalidades Azure Firewall Premium](premium-features.md)
