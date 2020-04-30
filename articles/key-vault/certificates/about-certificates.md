---
title: Sobre certificados de cofre de chaves Azure - Cofre de Chave Azure
description: Visão geral da interface e certificados rest do cofre de chaves Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 5e014634ecb251f05710de16daee30d72dae619e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81685901"
---
# <a name="about-azure-key-vault-certificates"></a>Sobre certificados azure key vault

O suporte de certificados Key Vault prevê a gestão dos seus certificados x509 e os seguintes comportamentos:  

-   Permite que um proprietário de certificado crie um certificado através de um processo de criação do Key Vault ou através da importação de um certificado existente. Inclui certificados auto-assinados e certificados gerados pela Autoridade de Certificados.
-   Permite que um proprietário de certificado Key Vault implemente armazenamento e gestão seguros de certificados X509 sem interação com material chave privado.  
-   Permite que um proprietário de certificado crie uma política que direcione o Key Vault para gerir o ciclo de vida de um certificado.  
-   Permite que os proprietários de certificados forneçam informações de contacto para notificação sobre eventos de ciclo de vida de expiração e renovação de certificado.  
-   Suporta a renovação automática com emitentes selecionados - Parceiro Key Vault X509 fornecedores de certificados /autoridades de certificados.

>[!Note]
>Os prestadores/autoridades não parceiros também são autorizados, mas não apoiarão a funcionalidade de renovação automática.

## <a name="composition-of-a-certificate"></a>Composição de um certificado

Quando um certificado key vault é criado, uma chave e segredo endereçados também são criados com o mesmo nome. A chave key vault permite operações chave e o segredo do Cofre chave permite a recuperação do valor do certificado como segredo. Um certificado Key Vault também contém metadados de certificados x509 públicos.  

O identificador e a versão dos certificados são semelhantes aos das chaves e segredos. Uma versão específica de uma chave endereçada e um segredo criado com a versão do certificado Key Vault está disponível na resposta do certificado Key Vault.
 
![Os certificados são objetos complexos](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Chave exportável ou não exportável

Quando um certificado key vault é criado, pode ser recuperado do segredo endereçado com a chave privada em formato PFX ou PEM. A política utilizada para a criação do certificado deve indicar que a chave é exportável. Se a apólice indica não ser exportável, então a chave privada não faz parte do valor quando recuperada como segredo.  

A chave endereçada torna-se mais relevante com certificados KV não exportáveis. As operações da chave KV endereçadas são mapeadas a partir do campo de *utilização* da chave da política de certificados KV utilizada para criar o Certificado KV.  

São suportados dois tipos de teclas – *RSA* ou *RSA HSM* com certificados. Exportável só é permitido com RSA, não suportado pela RSA HSM.  

## <a name="certificate-attributes-and-tags"></a>Atributos e Etiquetas de Certificado

Além dos metadados de certificados, uma chave endereçada e um segredo endereçado, um certificado Key Vault também contém atributos e tags.  

### <a name="attributes"></a>Atributos

Os atributos do certificado são espelhados em atributos da chave endereçada e do segredo criado quando o certificado KV é criado.  

Um certificado key vault tem os seguintes atributos:  

-   *ativado:* booleano, opcional, padrão é **verdade**. Pode ser especificado para indicar se os dados do certificado podem ser recuperados como secretos ou operáveis como chave. Também utilizado em conjunto com *nbf* e *exp* quando uma operação ocorre entre *nbf* e *exp*, e só será permitido se ativado for definido para verdade. As operações fora da *janela nbf* e *exp* são automaticamente proibidas.  

Existem atributos adicionais de leitura que estão incluídos em resposta:

-   *criado*: IntDate: indica quando esta versão do certificado foi criada.  
-   *atualizado*: IntDate: indica quando esta versão do certificado foi atualizada.  
-   *exp*: IntDate: contém o valor da data de validade do certificado x509.  
-   *nbf*: IntDate: contém o valor da data do certificado x509.  

> [!Note] 
> Se um certificado de cofre expirar, é a chave endereçada e o segredo torna-se inoperável.  

### <a name="tags"></a>Etiquetas

 O dicionário especificado pelo cliente de pares de valor-chave, semelhante a etiquetas em chaves e segredos.  

 > [!Note]
> As etiquetas são legíveis por um chamador se tiverem a *lista* ou *obterem* permissão para esse tipo de objeto (chaves, segredos ou certificados).

## <a name="certificate-policy"></a>Política de certificados

Uma política de certificados contém informações sobre como criar e gerir o ciclo de vida de um certificado key vault. Quando um certificado com chave privada é importado para o cofre chave, uma política padrão é criada através da leitura do certificado x509.  

Quando um certificado key vault é criado de raiz, uma apólice precisa de ser fornecida. A política especifica como criar esta versão de certificado Key Vault, ou a próxima versão do certificado Key Vault. Uma vez estabelecida uma política, não é necessária com sucessivas operações de criação para futuras versões. Só há um exemplo de uma apólice para todas as versões de um certificado key vault.  

A um nível elevado, uma política de certificados contém as seguintes informações:  

-   Propriedades do certificado X509: Contém nome sujeito, nomes alternativos sujeitos e outras propriedades usadas para criar um pedido de certificado x509.  
-   Propriedades-chave: contém o tipo de chave, o comprimento da chave, os campos-chave exportáveis e reutilizar os campos-chave. Estes campos instruem o cofre chave sobre como gerar uma chave.  
-   Propriedades secretas: contém propriedades secretas, tais como o tipo de conteúdo de segredo endereçado para gerar o valor secreto, para recuperar certificado como segredo.  
-   Ações vitalícias: contém ações vitalícias para o Certificado KV. Cada ação vitalícia contém:  

     - Gatilho: especificado através de dias antes da expiração ou percentagem de tempo de vida  

     - Ação: especificar tipo de ação – *emailContactos ou* *renovar automaticamente*  

-   Emitente: Parâmetros sobre o emitente do certificado a utilizar para emitir certificados x509.  
-   Atributos políticos: contém atributos associados à política  

### <a name="x509-to-key-vault-usage-mapping"></a>Mapeamento de utilização do Cofre-Chave X509 para Key Vault

O quadro seguinte representa o mapeamento da política de utilização chave x509 para operações-chave eficazes de uma chave criada como parte de uma criação de certificado key vault.

|**Bandeiras de utilização da chave X509**|**Operações chave do cofre chave**|**Comportamento predefinido**|
|----------|--------|--------|
|DataEncipherment|encriptar, desencriptar| N/D |
|Decifração|desencriptação| N/D  |
|Assinatura Digital|assinar, verificar| Padrão do cofre chave sem uma especificação de utilização no tempo de criação do certificado | 
|EncipherOnly|encrypt| N/D |
|KeyCertSign|assinar, verificar|N/D|
|KeyEncipherment|wrapKey, desembrulharKey| Padrão do cofre chave sem uma especificação de utilização no tempo de criação do certificado | 
|Não Repudiação|assinar, verificar| N/D |
|crlsign|assinar, verificar| N/D |

## <a name="certificate-issuer"></a>Emitente de certificado

Um objeto de certificado Key Vault contém uma configuração utilizada para comunicar com um fornecedor de emitente de certificado selecionado para encomendar certificados x509.  

-   Parceiros Key Vault com seguintes fornecedores de emitentes de certificados para certificados TLS/SSL

|**Nome do fornecedor**|**Localizações**|
|----------|--------|
|DigiCert|Apoiado em todos os principais locais de serviço de cofres em nuvem pública e Governo Azure|
|GlobalSign|Apoiado em todos os principais locais de serviço de cofres em nuvem pública e Governo Azure|

Antes que um emitente de certificado possa ser criado num Cofre chave, seguindo os passos pré-requisitos 1 e 2 deve ser realizado com sucesso.  

1. Fornecedores de Abordo da Autoridade de Certificados (CA)  

    -   Um administrador da organização deve embarcar na sua empresa (ex. Contoso) com pelo menos um fornecedor de CA.  

2. Administrador cria credenciais de solicitadores para key vault para inscrever (e renovar) certificados TLS/SSL  

    -   Fornece a configuração a ser usada para criar um objeto emitente do fornecedor no cofre chave  

Para obter mais informações sobre a criação de objetos Emitentes a partir do portal certificates, consulte o [blog Key Vault Certificates](https://aka.ms/kvcertsblog)  

O Key Vault permite a criação de múltiplos objetos emitentes com configuração diferente do fornecedor de emitentes. Uma vez criado um objeto emitente, o seu nome pode ser referenciado em uma ou múltiplas políticas de certificado. A referência ao objeto emitente instrui o Key Vault a utilizar a configuração conforme especificado no objeto emitente ao solicitar o certificado x509 ao fornecedor de CA durante a criação e renovação do certificado.  

Os objetos emitentes são criados no cofre e só podem ser usados com certificados KV no mesmo cofre.  

## <a name="certificate-contacts"></a>Contactos de certificados

Os contactos do certificado contêm informações de contacto para enviar notificações desencadeadas por eventos de vida de certificado. A informação de contactos é partilhada por todos os certificados do cofre chave. Uma notificação é enviada para todos os contactos especificados para um evento para qualquer certificado no cofre chave.  

Se a política de um certificado for definida para a renovação automática, então uma notificação é enviada nos seguintes eventos.  

- Antes da renovação do certificado
- Após a renovação do certificado, indicando se o certificado foi renovado com sucesso, ou se houve um erro, exigindo a renovação manual do certificado.  

  Quando uma política de certificadoque está definida para ser renovada manualmente (apenas por e-mail), uma notificação é enviada quando é hora de renovar o certificado.  

## <a name="certificate-access-control"></a>Controlo de Acesso ao Certificado

 O controlo de acesso aos certificados é gerido pela Key Vault, e é fornecido pelo Cofre chave que contém esses certificados. A política de controlo de acesso para certificados é distinta das políticas de controlo de acesso para chaves e segredos no mesmo Cofre chave. Os utilizadores podem criar um ou mais cofres para deter certificados, para manter o cenário de segmentação e gestão adequada dos certificados.  

 As seguintes permissões podem ser usadas, por principal, nos segredos acesso à entrada de controlo de acesso num cofre chave, e espelham de perto as operações permitidas num objeto secreto:  

- Permissões para operações de gestão de certificados
  - *obter*: Obtenha a versão de certificado atual, ou qualquer versão de um certificado 
  - *lista*: Enumerar os certificados em vigor ou versões de um certificado  
  - *atualização*: Atualizar um certificado
  - *criar*: Criar um certificado de cofre chave
  - *importação*: Material de certificado de importação num certificado key vault
  - *eliminar*um certificado, a sua política e todas as suas versões  
  - *recuperar*: Recuperar um certificado eliminado
  - *backup*: Backup um certificado em um cofre chave
  - *restaurar*: Restaurar um certificado de back-up para um cofre chave
  - *contactos de gestão*: Gerir os contactos de certificado saqueado chave  
  - *managingem emitentes*: Gerir as autoridades/emitentes do cofre chave
  - *getemememem*: Obter autoridades/emitentes de certificado
  - *enumeradores*: Lista das autoridades/emitentes de um certificado  
  - *setemedrs*: Criar ou atualizar as autoridades/emitentes do certificado Key Vault  
  - *eliminar emitentes*: Eliminar as autoridades/emitentes do certificado Key Vault  
 
- Permissões para operações privilegiadas
  - *Purga*: Purga (excluir permanentemente) um certificado eliminado

Para mais informações, consulte as operações do [Certificado na referência aAPI do Cofre Chave](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres - Atualizar a Política](/rest/api/keyvault/vaults/updateaccesspolicy)de Acesso .

## <a name="next-steps"></a>Passos seguintes

- [Sobre o Key Vault](../general/overview.md)
- [Acerca de chaves, segredos e certificados](../general/about-keys-secrets-certificates.md)
- [Acerca de chaves](../keys/about-keys.md)
- [Acerca de segredos](../secrets/about-secrets.md)
- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Cofre de Chaves](../general/developers-guide.md)