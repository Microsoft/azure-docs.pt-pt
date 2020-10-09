---
title: O que há de novo para Azure Key Vault
description: Atualizações recentes para Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 194b0122987d4fdc5d100112c60006588d28f96c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826924"
---
# <a name="whats-new-for-azure-key-vault"></a>Novidades para Azure Key Vault

Eis as novidades com o Cofre da Chave Azure. Novas funcionalidades e melhorias também são anunciadas no [canal Azure updates Key Vault](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="july-2020"></a>Julho de 2020

> [!WARNING]
> Estas duas atualizações têm o potencial de impactar as implementações do Azure Key Vault.

### <a name="soft-delete-on-by-default"></a>Excluir suavemente por padrão

Até ao final de 2020, o **soft-delete estará ligado por defeito para todos os cofres-chave**, tanto novos como pré-existentes. Para obter detalhes completos sobre esta potencial mudança, bem como passos para encontrar cofres-chave afetados e atualizá-los previamente, consulte o artigo [Soft-delete será ativado em todos os cofres chave](soft-delete-change.md). 

### <a name="azure-tls-certificate-changes"></a>Alterações no certificado Azure TLS  

A Microsoft está a atualizar os serviços da Azure para utilizar certificados TLS de um conjunto diferente de Autoridades de Certificados De Raiz (CAs). Esta alteração está a ser feita porque os certificados ac atuais [não cumprem um dos requisitos de base do Ca/Browser Forum.](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)

### <a name="when-will-this-change-happen"></a>Quando é que esta mudança vai acontecer?

- Os serviços [do Azure Ative Directory](/azure/active-directory) (Azure AD) iniciaram esta transição a 7 de julho de 2020.
- Todos os pontos finais Azure TLS/SSL recentemente criados contêm certificados atualizados acorrentados aos novos CAs raiz.
- Os pontos finais do Azure existentes vão transitar de forma faseada a partir de 13 de agosto de 2020 e completar-se-ão até 26 de outubro de 2020.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) e [DPS](/azure/iot-dps/) permanecerão na Baltimore CyberTrust Root CA, mas os seus CAs intermédios mudarão. Para mais detalhes, consulte o post blob [Azure IoT TLS: As mudanças estão a chegar! (... e por que se importa)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [O Azure Storage](/azure/storage) permanecerá na Baltimore CyberTrust Root CA, mas os seus CAs intermédios vão mudar. Para mais detalhes, consulte o post blob [Azure Storage TLS: Estão a chegar alterações! (... e por que se importa)](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).

> [!IMPORTANT]
> Os clientes poderão ter de atualizar as suas aplicações após esta alteração para evitar falhas de conectividade ao tentarem ligar-se aos serviços Azure.

### <a name="what-is-changing"></a>O que está a mudar?

Hoje, a maioria dos certificados TLS utilizados pela cadeia de serviços Azure até à seguinte Root CA:

| Nome comum da AC | Impressão digital (SHA1) |
|--|--|
| [Raiz cybertrust de Baltimore](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a5082c78db2852cae474 |

Os certificados TLS utilizados pelos serviços Azure irão acorrentar até um dos seguintes CAs raiz:

| Nome comum da AC | Impressão digital (SHA1) |
|--|--|
| [Raiz Global DigiCert G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9f666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d6d6d40c6dd2fb19c5436 |
| [Raiz cybertrust de Baltimore](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a5082c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Quando posso retirar a velha impressão digital intermediária?

Os certificados de CA em vigor *só* serão revogados a 15 de fevereiro de 2021. Depois dessa data, pode remover as impressões digitais antigas do seu código.

Se esta data mudar, será notificado da nova data de revogação.

### <a name="will-this-change-affect-me"></a>Esta mudança vai afetar-me? 

Esperamos que **a maioria dos clientes da Azure não** sejam impactados.  No entanto, a sua aplicação pode ser impactada se especificar explicitamente uma lista de AA aceitáveis. Esta prática é conhecida como pinning certificado.

Aqui estão algumas formas de detetar se a sua aplicação é impactada:

- Procure o seu código fonte para obter a impressão digital, nome comum e outras propriedades cert de qualquer um dos CAs do Microsoft IT TLS [encontrados aqui](https://www.microsoft.com/pki/mscorp/cps/default.htm). Se houver uma correspondência, a sua aplicação será impactada. Para resolver este problema, a atualização do código fonte inclui os novos CAs. Como boas práticas, certifique-se de que os CAs podem ser adicionados ou editados em curto prazo. Os regulamentos da indústria exigem que os certificados de CA sejam substituídos no prazo de sete dias e, por conseguinte, os clientes que confiam na fixação precisam de reagir rapidamente.

- Se tiver uma aplicação que se integre com APIs Azure ou outros serviços Azure e não tem a certeza se utiliza a fixação de certificados, consulte o fornecedor de aplicações.

- Diferentes sistemas operativos e tempos de execução linguísticos que comunicam com os serviços da Azure podem exigir medidas adicionais para construir corretamente a cadeia de certificados com estas novas raízes:
    - **Linux**: Muitas distribuições requerem que adicione CAs a /etc/ssl/certs. Para instruções específicas, consulte a documentação da distribuição.
    - **Java**: Certifique-se de que a loja de chaves Java contém os CAs listados acima.
    - **Windows running in disconnected environments**: Os sistemas que estão a funcionar em ambientes desligados terão de ter as novas raízes adicionadas à loja Trusted Root Certification Authorities e os intermediários adicionados à loja Das Autoridades de Certificação Intermédia.
    - **Android**: Verifique a documentação do seu dispositivo e versão do Android.
    - **Outros dispositivos de hardware, especialmente ioT**: Contacte o fabricante do dispositivo.

- Se tiver um ambiente onde as regras de firewall são definidas para permitir chamadas de saída apenas para locais específicos de verificação da Lista de Revogação de Certificados (CRL) e/ou protocolo de verificação do certificado online (OCSP). Você precisará permitir os seguintes URLs CRL e OCSP:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46rede de&#46;de ;d-trust
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com

## <a name="june-2020"></a>Junho de 2020

O Monitor Azure para o Cofre de Chaves está agora em pré-visualização.  O Azure Monitor fornece uma monitorização abrangente dos seus cofres chave, proporcionando uma visão unificada dos seus pedidos de Cofre chave, desempenho, falhas e latência. Para obter mais informações, consulte [O Monitor Azure para o Cofre de Chaves (pré-visualização).](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>Maio de 2020

O Key Vault "bring your own key" (BYOK) está agora geralmente disponível. Consulte a [especificação Azure Key Vault BYOK](../keys/byok-specification.md)e aprenda a [importar chaves protegidas pelo HSM para o Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Março de 2020

Pontos finais privados agora disponíveis na pré-visualização. O Azure Private Link Service permite-lhe aceder ao Azure Key Vault e aos serviços de cliente/parceiro hospedados no Azure durante um Private Endpoint na sua rede virtual.  Saiba como integrar o [Cofre de Chaves com a Ligação Privada Azure](private-link-service.md).

## <a name="2019"></a>2019

- Lançamento da próxima geração Azure Key Vault SDKs. Por exemplo, veja os quickstarts secretos do Azure Key Vault para [Python](../secrets/quick-create-python.md), [.NET,](../secrets/quick-create-net.md) [Java](../secrets/quick-create-java.md)e [Node.js](../secrets/quick-create-node.md)
- Novas políticas da Azure para gerir certificados de cofre chave. Consulte as [definições de Azure Policy incorporadas para o Cofre de Chaves](../policy-samples.md).
- Extensão da máquina virtual Azure Key Vault agora geralmente disponível.  Consulte [a extensão da máquina virtual Key Vault para](../../virtual-machines/extensions/key-vault-linux.md) a [extensão da máquina virtual](../../virtual-machines/extensions/key-vault-windows.md)Linux e Key Vault para windows .
- Gestão de segredos orientados para eventos para Azure Key Vault agora disponível em Azure Event Grid. Para obter mais informações, consulte [o esquema da Grelha de Eventos para eventos no Cofre da Chave Azure](.. /.. /event-grid/event-schema-key-vault.md], e aprender a [receber e responder às notificações-chave do cofre com a Azure Event Grid](event-grid-tutorial.md).

## <a name="2018"></a>2018

Novas funcionalidades e integrações lançadas este ano:

- Integração com Funções Azure. Para um cenário de aproveitamento das [funções do Azure](../../azure-functions/index.yml) para operações de cofre chave, consulte [Automatizar a rotação de um segredo](../secrets/tutorial-rotation.md). 
- [Integração com a Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Com isto, a Azure Databricks suporta agora dois tipos de âmbitos secretos: Azure Key Vault apoiado e apoiado por Databricks. Para obter mais informações, consulte [Criar um âmbito secreto apoiado por Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Pontos finais de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Novas funcionalidades lançadas este ano:

- Chaves de conta de armazenamento geridas. Recurso De Chaves de Conta de Armazenamento acrescentou uma integração mais fácil com o Azure Storage. Consulte o tópico geral para obter mais informações, [visão geral das chaves de conta de armazenamento geridos](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).
- Apagar suavemente. A função de eliminação suave melhora a proteção de dados dos cofres e dos objetos chave do cofre. Consulte o tópico geral para obter mais informações, eliminar a visão geral do [soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015"></a>2015

Novas funcionalidades lançadas este ano:
- Gestão de certificados. Adicionado como recurso à versão GA 2015-06-01 no dia 26 de setembro de 2016.

A Disponibilidade Geral (versão 2015-06-01) foi anunciada em 24 de junho de 2015. Foram efetuadas as seguintes alterações nesta versão: 
- Eliminar uma chave - campo de "utilização" removido.
- Obtenha informações sobre uma chave - campo de "uso" removido.
- Importe uma chave para um cofre - campo de "uso" removido.
- Restaurar uma chave - campo de "utilização" removido.     
- Alterou "RSA_OAEP" para "RSA-OAEP" para algoritmos RSA. Veja [mais sobre chaves, segredos e certificados.](about-keys-secrets-certificates.md)    
 
A segunda versão de pré-visualização (versão 2015-02-01)foi anunciada a 20 de abril de 2015. Para obter mais informações, consulte o post de blog [REST API Update.](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) Foram atualizadas as seguintes tarefas:
 
- Lista as chaves num cofre- apoio de paginação adicionado à operação.
- Liste as versões de uma chave - operação adicionada para listar as versões de uma chave.  
- List secrets in a vault - adicionado suporte de paginação.
- List versions of a secret - adicione operação para listar as versões de um segredo.  
- Todas as operações - Adicionados os tempos criados/atualizados aos atributos.  
- Crie um segredo - adicione conteúdo-tipo aos segredos.
- Crie uma chave - etiquetas adicionadas como informação opcional.
- Crie um segredo - etiquetas adicionadas como informação opcional.
- Atualizar uma chave - etiquetas adicionadas como informação opcional.
- Atualize um segredo - etiquetas adicionadas como informação opcional.
- Mudou o tamanho máximo para segredos de 10 K a 25 K Bytes. Ver, [sobre chaves, segredos e certificados.](about-keys-secrets-certificates.md)    

## <a name="2014"></a>2014
 
A primeira versão de pré-visualização (versão 2014-12-08)foi anunciada a 8 de janeiro de 2015.  

## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas adicionais, contacte-nos através [do suporte.](https://azure.microsoft.com/support/options/)  
