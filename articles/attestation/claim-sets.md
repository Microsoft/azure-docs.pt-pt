---
title: Conjuntos de reivindicação de atestado de Azure
description: Os conjuntos de reivindicações de Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517560"
---
# <a name="claim-sets"></a>Conjuntos de afirmações

As reclamações geradas no processo de atestar enclaves utilizando o Microsoft Azure Attestation podem ser divididas nas categorias abaixo:

- **Alegações recebidas**: As alegações geradas pela Microsoft Azure Attestation após analisar as provas do atestado e podem ser usadas por autores de políticas para definir regras de autorização numa política personalizada

- **Reclamações cessantes**: As alegações geradas pela Azure Attestation e incluídas no token do atestado

- **Reclamações de propriedade**: As reclamações criadas como uma saída pela Azure Attestation. Contém todas as alegações que representam propriedades do token do atestado, tais como a codificação do relatório, a duração da validade do relatório, e assim por diante.

## <a name="incoming-claims"></a>Reclamações de entrada 

### <a name="sgx-attestation"></a>Atestado SGX

Alegações a utilizar por autores de políticas para definir regras de autorização numa política de atestado SGX:

- **x-ms-sgx-is-debuggable**: Um valor booleano, que indica se a depuração do enclave está ativada ou não.
  
  Os enclaves SGX podem ser carregados com depuração desativada ou ativado. Quando a bandeira é verdadeira no enclave, permite depurar características para o código enclave. Isto inclui a capacidade de aceder à memória do enclave. Por isso, recomenda-se que a bandeira seja verdadeira apenas para fins de desenvolvimento. Se estiver ativado em ambiente de produção, as garantias de segurança da SGX não serão mantidas.
  
  Os utilizadores do Azure Attestation podem usar a política de atestado para verificar se a depuração é desativada para o enclave SGX. Uma vez adicionada a regra da política, o atestado falhará quando um utilizador malicioso liga o suporte de depuração para ter acesso ao conteúdo do enclave.

- **x-ms-sgx-produto-id**: Um valor inteiro, que indica o ID do produto do enclave SGX.

  O autor do enclave atribui um ID de produto a cada enclave. O ID do produto permite ao autor do enclave segmentar enclaves assinados usando o mesmo MRSIGNER. Ao adicionar uma regra de validação na política de atestado, os clientes podem verificar se estão a usar os enclaves pretendidos. O Attestation falhará se o ID do produto do enclave não corresponder ao valor publicado pelo autor do enclave.

- **x-ms-sgx-mrsigner**: Um valor de corda, que identifica o autor do enclave SGX.

  MRSIGNER é o haxixe da chave pública do autor do enclave que é usado para assinar o binário enclave. Ao validar a MRSIGNER através de uma política de atestado, os clientes podem verificar se os binários de confiança estão a funcionar dentro de um enclave. Quando a alegação política não corresponde à MRSIGNER do autor do enclave, implica que o binário enclave não é assinado por uma fonte de confiança e que o atestado falha.
  
  Quando um autor do enclave prefere rodar MRSIGNER por razões de segurança, a política de Azure Attestation deve ser atualizada para apoiar os novos e antigos valores MRSIGNER antes da atualização dos binários. Caso contrário, os controlos de autorização falharão, resultando em falhas no atestado.
  
  A política de atestado deve ser atualizada utilizando o formato abaixo. 
 
  #### <a name="before-key-rotation"></a>Antes da rotação da chave
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>Durante a rotação da chave

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>Após a rotação da chave

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mrenclave**: Um valor de cadeia, que identifica o código e os dados carregados na memória do enclave. 

  MrenCLAVE é uma das medições do enclave que pode ser usada para verificar os binários do enclave. É o haxixe do código que corre dentro do enclave. A medição muda a cada alteração ao código binário do enclave. Ao validar o MRENCLAVE através de uma política de atestado, os clientes podem verificar se os binários pretendidos estão a decorrer dentro de um enclave. No entanto, uma vez que se espera que o MRENCLAVE mude frequentemente com qualquer alteração trivial ao código existente, recomenda-se verificar binários enclaves utilizando a validação MRSIGNER numa política de atestação.

- **x-ms-sgx-svn**: Um valor inteiro, que indica o número da versão de segurança do enclave SGX

  O autor do enclave atribui um Número de Versão de Segurança (SVN) a cada versão do enclave SGX. Quando um problema de segurança é descoberto no código do enclave, o autor do enclave incrementa a correção de vulnerabilidade pós-vulnerabilidade de valor SVN. Para evitar interagir com o código enclave inseguro, os clientes podem adicionar uma regra de validação na política de atestado. Se o SVN do código enclave não corresponder à versão recomendada pelo autor do enclave, o atestado falhará.

Abaixo as reclamações são consideradas depreciadas, mas são totalmente apoiadas e continuarão a ser incluídas no futuro. Recomenda-se a utilização dos nomes de reclamações não depreciados.

Reivindicação precotado | Reclamação recomendada
--- | --- 
$is-depurável | x-ms-sgx-é-debuggável
$product id | x-ms-sgx-produto-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>Atestado de TPM

Alegações a utilizar por autores de políticas para definir regras de autorização numa política de atestado tpm:

- **aikValidated**: Valor booleano contendo informações se a chave de identidade atesta (AIK) cert tiver sido validada ou não
- **aikPubHash**: Cadeia contendo a base64 (chave pública AIK em formato DER))
- **tpmVersion**: Valor inteiro contendo a versão principal do Módulo plataforma fidedigna (TPM)
- **secureBootEnabled**: Valor booleano para indicar se a bota segura está ativada
- **iommuEnabled**: Valor booleano para indicar se a unidade de gestão da memória de saída de entrada (Iommu) está ativada
- **bootDebuggingDisabled**: Valor booleano para indicar se a depuração de botas é desativada
- **notSafeMode**: Valor booleano para indicar se o Windows não está a funcionar em modo de segurança
- **notWinPE**: Valor booleano indicando se o Windows não está a funcionar no modo WinPE
- **vbsEnabled**: Valor booleano indicando se VBS está ativado
- **vbsReportPresent**: Valor booleano indicando se o relatório do enclave VBS está disponível

### <a name="vbs-attestation"></a>Atestado VBS

Além das alegações de política de atestados TPM, as alegações abaixo podem ser usadas por autores de políticas para definir regras de autorização numa política de atestação VBS.

- **enclaveAuthorId**: Valor de corda que contém o valor codificado Base64Url do id-O identificador autor do módulo primário para o enclave
- **enclaveImageId**: Valor de corda que contém o valor codificado Base64Url do id-Imagem do enclave O identificador de imagem do módulo primário para o enclave
- **enclaveOwnerId**: Valor de corda que contém o valor codificado Base64Url do id proprietário do enclave O identificador do proprietário para o enclave
- **enclaveFamilyId**: Valor de corda que contém o valor codificado Base64Url do ID da família do enclave. O identificador da família do módulo primário para o enclave
- **enclaveSvn**: Valor inteiro contendo o número da versão de segurança do módulo primário para o enclave
- **enclavePlatformSvn**: Valor inteiro contendo o número da versão de segurança da plataforma que acolhe o enclave
- **enclaveFlags**: A reivindicação do enclaveFlags é um valor inteiro contendo bandeiras que descrevem a política de tempo de execução para o enclave

## <a name="outgoing-claims"></a>Reclamações de saída 

### <a name="common-for-all-attestation-types"></a>Comum para todos os tipos de atestado

O Azure Attestation inclui as alegações abaixo no token de atestado para todos os tipos de atestado. 

- **x-ms-ver**: versão do esquema JWT (espera-se que seja "1.0")
- **x-ms-attestation-type**: Valor de corda que representa o tipo de atestado 
- **x-ms-policy-hash**: Política de avaliação do azure atestado calculada como BASE64URL(SHA256(UTF8(BASE64URL(UTF8(texto de política))))
- **x-ms-policy-signer**: Objeto JSON com um membro "jwk" representando a chave que um cliente usou para assinar a sua política. Isto é aplicável quando o cliente faz o upload de uma apólice assinada

Abaixo os nomes de reclamação são usados a partir da [especificação JWT IETF](https://tools.ietf.org/html/rfc7519)

- **"jti" (JWT ID) Claim** - Identificador exclusivo para o JWT
- **"iss" (Emitente) Claim** - O principal que emitiu o JWT 
- **"iat" (Emitido Em) Reivindicação** - O momento em que o JWT foi emitido em 
- **"exp" (Prazo de Expiração) Reivindicação** - Prazo de validade após o qual o JWT não deve ser aceite para processamento
- **"nbf" (Não Antes) Reivindicação** - Não antes do tempo anterior ao qual o JWT não deve ser aceite para processamento 

Abaixo os nomes de reclamação são usados a partir do projeto de [especificação IETF EAT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Nonce claim" (nonce)** - Uma cópia direta não transaformada de um valor opcional fornecido por um cliente 

Abaixo as reclamações são consideradas depreciadas, mas são totalmente apoiadas e continuarão a ser incluídas no futuro. Recomenda-se a utilização dos nomes de reclamações não depreciados.

Reivindicação precotado | Reclamação recomendada
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
policy_hash | x-ms-política-haxixe
maa-políticaHash | x-ms-política-haxixe
policy_signer  | x-ms-política-signer

### <a name="sgx-attestation"></a>Atestado SGX 

Abaixo as reclamações são geradas e incluídas no token atestado pelo serviço para atestado SGX.

- **x-ms-sgx-is-debuggable**: A Boolean, que indica se o enclave tem ou não depuração ativado
- **x-ms-sgx-produto-id**: Valor de ID do produto do enclave SGX 
- **x-ms-sgx-mrsigner**: valor codificado hex do campo "mrsigner" da citação
- **x-ms-sgx-mrenclave**: valor codificado hex do campo "mrenclave" da citação
- **x-ms-sgx-svn**: número da versão de segurança codificado na cotação 
- **x-ms-sgx-ehd**: dados detidos pelo enclave formatados como BASE64URL (dados detidos pelo enclave)
- **x-ms-sgx-colateral**: objeto JSON descrevendo a garantia usada para realizar atestado. O valor para a alegação x-ms-sgx-colateral é um objeto JSON aninhado com os seguintes pares de tecla/valor:
    - **qeidcertshash**: valor SHA256 de Citeing Enclave (QE) Certificações de emissão de identidade
    - **qeidcrlhash**: valor SHA256 da lista de certs CRL de emissão de identidade QE
    - **qeidhash**: valor SHA256 da garantia de identidade QE
    - **quotehash**: VALOR SHA256 da cotação avaliada
    - **tcbinfocertshash**: valor SHA256 dos certificados de emissão de informações TCB
    - **tcbinfocrlhash**: valor SHA256 da lista de certs CRL da TCB Info
    - **tbinfohash**: valor SHA256 da garantia de informação TCB

Abaixo as reclamações são consideradas depreciadas, mas são totalmente apoiadas e continuarão a ser incluídas no futuro. Recomenda-se a utilização dos nomes de reclamações não depreciados.

Reivindicação precotado | Reclamação recomendada
--- | --- 
$is-depurável | x-ms-sgx-é-debuggável
$product id | x-ms-sgx-produto-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-atesstationcollateral | x-ms-sgx-colateral

### <a name="tpm-and-vbs-attestation"></a>Atestado de TPM e VBS

- **cnf (Confirmação)**: A alegação "cnf" é utilizada para identificar a chave de prova de posse. A alegação de confirmação, tal como definida no RFC 7800, contém a parte pública da chave de enclave atesta representada como um objeto JSON Web Key (JWK) (RFC 7517)
- **rp_data (dados da parte)**: Confiar nos dados das partes, se houver, especificados no pedido, utilizados pela parte que conta como um nó para garantir a frescura do relatório. rp_data só é adicionado se houver rp_data

## <a name="property-claims"></a>Reclamações imobiliárias

### <a name="tpm-and-vbs-attestation"></a>Atestado de TPM e VBS

- **report_validity_in_minutes**: Uma alegação completa para significar por quanto tempo o token é válido.
  - **Valor predefinido(tempo)**: Um dia em minutos.
  - **Valor máximo(tempo)**: Um ano em minutos.
- **omit_x5c**: Uma alegação booleana que indique se o Azure Attestation deve omitir o certificado utilizado para fornecer um comprovativo da autenticidade do serviço. Se for verdade, x5t será adicionado ao token do atestado. Se falso(predefinitivo), x5c será adicionado ao token do atestado.

## <a name="next-steps"></a>Passos seguintes
- [Como autor e assinar uma política de atestado](author-sign-policy.md)
- [Configurar a Azure Attestation usando o PowerShell](quickstart-powershell.md)
