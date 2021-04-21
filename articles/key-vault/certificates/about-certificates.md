---
title: Sobre certificados de cofre chave Azure - Cofre de Chaves Azure
description: Visão geral da interface e certificados do Azure Key Vault REST.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: fb69068ddac311a8020a76eec9b18fab3256fea6
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752539"
---
# <a name="about-azure-key-vault-certificates"></a>Acerca dos certificados do Azure Key Vault

O suporte aos certificados Key Vault prevê a gestão dos seus certificados x509 e os seguintes comportamentos:  

-   Permite que um proprietário de certificados crie um certificado através de um processo de criação do Cofre-Chave ou através da importação de um certificado existente. Inclui certificados auto-assinados e certificados gerados pela Autoridade de Certificados.
-   Permite que um proprietário de certificado Key Vault implemente armazenamento e gestão segura de certificados X509 sem interação com material chave privado.  
-   Permite que um proprietário de certificados crie uma política que direcione o Key Vault para gerir o ciclo de vida de um certificado.  
-   Permite que os proprietários de certificados forneçam informações de contacto para notificação sobre eventos de ciclo de vida de expiração e renovação de certificado.  
-   Suporta a renovação automática com emitentes selecionados - Key Vault partner X509 fornecedores de certificados /autoridades de certificados.

>[!Note]
>Os fornecedores/autoridades não parceiros também são permitidos, mas não apoiarão a funcionalidade de renovação automática.

## <a name="composition-of-a-certificate"></a>Composição de um Certificado

Quando um certificado Key Vault é criado, uma chave e segredo endereçada também são criados com o mesmo nome. A chave Key Vault permite operações chave e o segredo do Cofre chave permite a recuperação do valor do certificado como segredo. Um certificado Key Vault também contém metadados de certificados x509 públicos.  

O identificador e a versão dos certificados são semelhantes aos das chaves e segredos. Uma versão específica de uma chave endereçada e secreta criada com a versão do certificado Key Vault está disponível na resposta do certificado Key Vault.
 
![Certificados são objetos complexos](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Chave exportável ou não exportável

Quando um certificado Key Vault é criado, pode ser recuperado do segredo endereçado com a chave privada em formato PFX ou PEM. A política utilizada para a criação do certificado deve indicar que a chave é exportável. Se a apólice indica não ser exportável, então a chave privada não é uma parte do valor quando recuperada como um segredo.  

A chave endereçada torna-se mais relevante com certificados KV não exportáveis. As operações da chave KV endereçada são mapeadas a partir do campo de teclado da política de *certificados* KV utilizada para criar o Certificado KV.  

O tipo de par-chave suportado para certificados

 - Tipos-chave suportados: RSA, RSA-HSM, EC, EC-HSM, out (listado [aqui](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) Exportável só é permitido com RSA, CE. As chaves HSM não seriam exportáveis.

|Tipo de chave|Sobre|Segurança|
|--|--|--|
|**RSA**| Chave RSA "protegida por software"|FIPS 140-2 Nível 1|
|**RSA-HSM**| Chave RSA "protegida por HSM" (apenas Premium SKU)|FIPS 140-2 Nível 2 HSM|
|**EC**| Chave da curva elíptica "protegida por software"|FIPS 140-2 Nível 1|
|**EC-HSM**| Chave elíptica "protegida por HSM" (apenas Premium SKU)|FIPS 140-2 Nível 2 HSM|
|||

## <a name="certificate-attributes-and-tags"></a>Atributos e Etiquetas de Certificado

Além dos metadados de certificado, uma chave endereçada e segredo endereçada, um certificado Key Vault também contém atributos e tags.  

### <a name="attributes"></a>Atributos

Os atributos do certificado são espelhados em atributos da chave endereçada e do segredo criados quando o certificado KV é criado.  

Um certificado Key Vault tem os seguintes atributos:  

-   *ativado:* boolean, opcional, o padrão é **verdadeiro**. Pode ser especificado para indicar se os dados do certificado podem ser recuperados como secretos ou operáveis como uma chave. Também utilizado em conjunto com *a NBF* e *exp* quando ocorre uma operação entre *nbf* e *exp*, e só será permitido se ativado for definido como verdadeiro. As operações fora da janela *nbf* e *exp* são automaticamente proibidas.  

Existem atributos adicionais apenas de leitura que estão incluídos em resposta:

-   *criado*: IntDate: indica quando esta versão do certificado foi criada.  
-   *atualizado*: IntDate: indica quando esta versão do certificado foi atualizada.  
-   *exp*: IntDate: contém o valor da data de validade do certificado x509.  
-   *nbf*: IntDate: contém o valor da data do certificado x509.  

> [!Note] 
> Se um certificado do Key Vault expirar, a chave endereçável e o segredo deixarão de funcionar.  

### <a name="tags"></a>Etiquetas

 O dicionário especificado pelo cliente de pares de valores chave, semelhante a tags em teclas e segredos.  

 > [!Note]
> As etiquetas são legíveis por um autor da lista se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

## <a name="certificate-policy"></a>Política de certificados

Uma política de certificado contém informações sobre como criar e gerir o ciclo de vida de um certificado Key Vault. Quando um certificado com chave privada é importado para o cofre chave, uma política de predefinição é criada através da leitura do certificado x509.  

Quando um certificado Key Vault é criado de raiz, uma política precisa de ser fornecida. A política especifica como criar esta versão do certificado Key Vault ou a próxima versão do certificado Key Vault. Uma vez estabelecida uma política, não é necessária com operações de criação sucessivas para futuras versões. Há apenas um exemplo de uma apólice para todas as versões de um certificado key vault.  

A um nível elevado, uma política de certificados contém as seguintes informações (as suas definições podem ser encontradas [aqui):](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy)  

-   Propriedades do certificado X509: Contém nome de sujeito, nomes alternativos sujeitos e outras propriedades usadas para criar um pedido de certificado x509.  
-   Principais propriedades: contém o tipo de chave, comprimento da chave, exportável e reuseKeyOnRenewal. Estes campos instruem o cofre de chaves sobre como gerar uma chave. 
     - Tipos-chave suportados: RSA, RSA-HSM, EC, EC-HSM, out (listado [aqui)](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype) 
-   Propriedades secretas: contém propriedades secretas, tais como o tipo de conteúdo de segredo endereçada para gerar o valor secreto, para recuperar o certificado como um segredo.  
-   Ações vitalícias: contém ações vitalícias para o Certificado KV. Cada ação vitalícia contém:  

     - Gatilho: especificado por dias antes da expiração ou da percentagem de duração útil  

     - Ação: especificar tipo de ação – *emailContacts* ou *autoRenew*  

-   Emitente: Parâmetros sobre o emitente de certificado a utilizar para emitir certificados x509.  
-   Atributos de Política: contém atributos associados à política  

### <a name="x509-to-key-vault-usage-mapping"></a>Mapeamento de utilização X509 para Key Vault

A tabela seguinte representa o mapeamento da política de utilização chave x509 para operações-chave eficazes de uma chave criada como parte de uma criação de certificado Key Vault.

|**Bandeiras de utilização chave X509**|**Ops chave do cofre chave**|**Comportamento predefinido**|
|----------|--------|--------|
|DataEnciframentment|encriptar, desencriptar| N/D |
|Decifrar Apenas|desencriptar| N/D  |
|Assinatura Digital|assinar, verificar| Predefinição do Cofre chave sem especificação de utilização no tempo de criação de certificado | 
|EncipherOnly|encrypt| N/D |
|KeyCertSign|assinar, verificar|N/D|
|Chaveenciframent|wrapKey, desembrulhar| Predefinição do Cofre chave sem especificação de utilização no tempo de criação de certificado | 
|NãoRepudição|assinar, verificar| N/D |
|crlsign|assinar, verificar| N/D |

## <a name="certificate-issuer"></a>Emitente de Certificado

Um objeto de certificado Key Vault contém uma configuração usada para comunicar com um fornecedor de emitente de certificado selecionado para encomendar certificados x509.  

-   Parceiros-chave da Vault com os seguintes fornecedores de emitentes de certificados para certificados TLS/SSL

|**Nome do Fornecedor**|**Localizações**|
|----------|--------|
|DigiCert|Apoiado em todos os principais locais de serviço de cofres em nuvem pública e Governo de Azure|
|GlobalSign|Apoiado em todos os principais locais de serviço de cofres em nuvem pública e Governo de Azure|

Antes de um emitente de certificado poder ser criado num Cofre-Chave, os passos 1 e 2 pré-requisitos devem ser realizados com sucesso.  

1. A bordo dos fornecedores da Autoridade de Certificados (CA)  

    -   Um administrador da organização deve embarcar na sua empresa (ex. Contoso) com pelo menos um provedor de CA.  

2. Admin cria credenciais de solicitação para o Cofre-Chave para inscrever (e renovar) certificados TLS/SSL  

    -   Fornece a configuração a ser usada para criar um objeto emitente do fornecedor no cofre chave  

Para obter mais informações sobre a criação de objetos emitentes a partir do portal Certificados, consulte o [blog Certificados de Cofre chave](/archive/blogs/kv/manage-certificates-via-azure-key-vault)  

O Key Vault permite a criação de múltiplos objetos emitentes com configuração diferente do fornecedor de emitentes. Uma vez criado um objeto emitente, o seu nome pode ser referenciado numa ou em várias políticas de certificado. Referenciar o objeto emitente instrui o Key Vault a utilizar a configuração conforme especificado no objeto emitente ao solicitar o certificado x509 ao fornecedor ca durante a criação e renovação do certificado.  

Os objetos emitentes são criados no cofre e só podem ser utilizados com certificados KV no mesmo cofre.  

## <a name="certificate-contacts"></a>Contactos de Certificado

Os contactos do certificado contêm informações de contacto para enviar notificações desencadeadas por eventos de vida de certificado. A informação dos contactos é partilhada por todos os certificados no cofre chave. É enviada uma notificação a todos os contactos especificados para um evento para qualquer certificado no cofre de chaves. Para obter informações sobre como definir o contacto do Certificado, consulte [aqui](overview-renew-certificate.md#steps-to-set-certificate-notifications)  

## <a name="certificate-access-control"></a>Controlo de Acesso a Certificados

 O controlo de acesso para certificados é gerido pelo Key Vault e é disponibilizado pelo Key Vault que contém esses certificados. A política de controlo de acesso para certificados distingue-se das políticas de controlo de acesso para chaves e segredos no mesmo Cofre-Chave. Os utilizadores podem criar um ou mais cofres para deter certificados, para manter o cenário apropriado segmentação e gestão de certificados.  Para obter mais informações sobre o controlo do acesso ao certificado, consulte [aqui](certificate-access-control.md)

## <a name="next-steps"></a>Passos seguintes

- [Sobre o Key Vault](../general/overview.md)
- [Acerca de chaves, segredos e certificados](../general/about-keys-secrets-certificates.md)
- [Acerca de chaves](../keys/about-keys.md)
- [Acerca de segredos](../secrets/about-secrets.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)
