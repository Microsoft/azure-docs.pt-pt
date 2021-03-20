---
title: Perguntas mais frequentes
description: Respostas a perguntas frequentes sobre Microsoft Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 39f628845bdc9d54b48b1c8037f4a506a9d5c00a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89236710"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Perguntas frequentes para microsoft Azure Attestation

Este artigo fornece respostas a algumas das perguntas mais comuns sobre [a Azure Attestation](overview.md).

Se a sua questão Azure não for abordada neste artigo, também pode submeter um pedido de apoio da Azure na página de suporte do [Azure](https://azure.microsoft.com/support/options/).

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>O que é o serviço de caching Azure PCK e o seu papel na atestado de enclave

O serviço de caching Azure PCK define a linha de base de segurança Azure para os nós de [computação Azure Confidential (ACC)](../confidential-computing/overview.md) da Intel e caches os dados. As informações em cache serão ainda utilizadas pela Azure Attestation na validação de Ambientes de Execução Fidedigna (TEEs).  

Serviço de caching Azure PCK:
   - Oferece alta disponibilidade 
   - Reduz as dependências dos serviços hospedados externamente e da conectividade da Internet.
   - Obtém as versões mais recentes de certificados Intel, CRLs, Informações de Base de Computação Fidedigna (TCB) e citando a identidade do Enclave dos nós ACC da Intel. O serviço confirma, portanto, a linha de base de segurança Azure a ser remetida pela Azure Attestation enquanto valida as TEEs, reduzindo consideravelmente as falhas no atestado devido à invalidação ou revogação dos certificados da Intel  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>É atestado SGX suportado pela Azure Attestation em ambientes não-Azure

O Azure Attestation depende da linha de base de segurança indicada pelo serviço de caching Azure PCK para validar os TEEs. O serviço de caching Azure PCK foi atualmente concebido para suportar apenas nós de computação Azure Confidential. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Que validações a Azure Attestation atua para atestar enclaves SGX

O Azure Attestation é um quadro unificado para atestar remotamente diferentes tipos de TEEs. Atestado Azure:

   - Valida se a raiz de confiança de uma citação de enclave assinada pertence à Intel.
   - Valida se a citação do enclave satisfaça a linha de base de segurança Azure, tal como definida pelo serviço de caching Azure PCK.
   - Valida se o hash SHA256 do Enclave Held Data (EHD) no objeto de pedido de atestado corresponder aos primeiros 32 bytes do campo reportData na citação do enclave.
   - Permite que os clientes criem um fornecedor de atestado e configurem uma política personalizada. Além das validações acima referidas, a Azure Attestation avalia a citação do enclave contra a política. As políticas definem as regras de autorização para o enclave e também ditam regras de emissão para gerar o token do atestado. Para confirmar se o software pretendido está em execução num enclave, os clientes podem adicionar regras de autorização para verificar se os campos **de mrsigner** e **mrenclave** na cotação do enclave correspondem aos valores dos binários de clientes.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>Como pode um verificador obter a garantia para atestado SGX suportado pela Azure Attestation

Em geral, para os modelos de atestado com a Intel como a raiz da confiança, o cliente do attestation fala com as APIs do enclave para recolher as provas do enclave. As APIs do Enclave ligam internamente para o serviço de caching intel PCK para obter certificados Intel do nó a serem atestados. Os certificados são usados para assinar as provas do enclave, gerando assim uma garantia remotamente atestável.  

O mesmo processo pode ser implementado para a Azure Attestation. No entanto, para aproveitar os benefícios oferecidos pelo serviço de caching Azure PCK, após a instalação da máquina virtual ACC, é aconselhável instalar a [biblioteca Azure DCAP](https://www.nuget.org/packages/Microsoft.Azure.DCAP). Com base no acordo com a Intel, quando a biblioteca Azure DCAP é instalada, os pedidos de geração de provas do enclave são redirecionados do serviço de caching Intel PCK para o serviço de caching Azure PCK. A biblioteca Azure DCAP é suportada em ambientes baseados em Windows e Linux.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>Como mudar para a Azure Attestation de outros modelos de atestado

- Depois de instalar a máquina virtual de computação Azure Confidential, instale a biblioteca Azure DCAP[(Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux)](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)para aproveitar os benefícios oferecidos pelo serviço de caching Azure PCK.
- O cliente de atestado remoto precisa de ser autorizado que possa recuperar as provas do enclave e enviar pedidos para a Azure Attestation. Consulte [as amostras de código](/samples/browse/?expanded=azure&terms=attestation) para referência 
- Os pedidos de atestado podem ser enviados para o ponto final da API REST de fornecedores predefinidos ou fornecedores de atestados personalizados 
- As APIs Azure Attestation estão protegidas pela autenticação Azure AD. Daí que o cliente que invoca atestar APIs deve ser capaz de obter e passar um token de acesso AD válido AZure no pedido de atestado 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>Como pode a parte de confiar verificar a integridade do token atestado

O token de atestado gerado pela Azure Attestation é assinado com um certificado auto-assinado. Os certificados são expostos através de um [ponto final de metadados OpenID](/rest/api/attestation/metadataconfiguration/get). A parte de conta pode recuperar os certificados de assinatura deste ponto final e efetuar a verificação de assinatura do token do atestado. O tempo de validade do token do atestado é de 8 horas. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>Como identificar o certificado a utilizar para verificação de assinaturas a partir do ponto final de metadados OpenID

Vários certificados expostos no ponto final de metadados OpenID correspondem a diferentes casos de utilização (por exemplo, atestado SGX) suportados pela Azure Attestation. De acordo com as normas especificadas pelo [RFC 7515,](https://tools.ietf.org/html/rfc7515)o certificado com iD chave (criança) correspondente ao parâmetro *do miúdo* no cabeçalho de ficha de atestado deve ser utilizado para verificação de assinaturas. Se não for encontrada **nenhuma criança** correspondente, então espera-se que experimente todos os certificados expostos pelo ponto final de metadados OpenID.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>É possível que a parte que conta partilhe segredos com os Ambientes de Execução Fidedigna validados (TEEs)

A chave pública gerada dentro de um enclave pode ser expressa na propriedade de Dados Detidos do Enclave (EHD) do objeto de pedido de atestado enviado pelo cliente para a Azure Attestation. Depois de confirmar se o haxixe SHA256 de EHD é expresso no campo reportAData da citação, a Azure Attestation inclui EHD no token do atestado. A parte de confiamento pode usar o EHD a partir da resposta verificada para encriptar os segredos e partilhar com o enclave. Consulte [os conceitos básicos do Azure Attestation](basic-concepts.md) para obter mais informações.
