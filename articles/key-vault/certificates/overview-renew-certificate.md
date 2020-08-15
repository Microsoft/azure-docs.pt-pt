---
title: Sobre a renovação do certificado Azure Key Vault
description: Sobre a renovação do certificado Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: bbd65cdfc7c8ebbc08b914158577337cc49ec887
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236068"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>Sobre a renovação do certificado Azure Key Vault

O Azure Key Vault permite-lhe facilmente provisões, gestão e implementação de certificados digitais para a sua rede e para permitir comunicações seguras para aplicações. Para obter informações mais gerais sobre certificados, consulte [certificados de cofre chave Azure](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)

Ter certificado de curta duração ou aumentar a frequência de rotação do certificado limita o âmbito de danos do adversário.

## <a name="certificate-expiration-notifications"></a>Notificações de expiração do certificado
Em primeiro lugar, certifique-se de adicionar o contacto do Certificado ao seu Cofre-Chave para ser notificado quando os certificados estiverem prestes a expirar (por exemplo. utilizando o PowerShell [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)) Segundo, configurar quando pretender ser notificado sobre a expiração do certificado. Para configurar a ação de tempo de vida, siga [aqui](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)

Há três categorias de criação de certificados no cofre-chave. Este guia irá ajudá-lo a entender como a renovação de certificados pode ser alcançada.
-   Certificados criados com CA integrado (DigiCert ou GlobalSign)
-   Certificados criados com CA não integrado
-   Certificados auto-assinados

## <a name="renewal-of-integrated-ca-certificate"></a>Renovação do certificado integrado de CA 
Boas notícias! A Azure Key Vaults cuida da manutenção de certificados que são emitidos pela Microsoft confiáveis CAs, ou seja, DigiCert e GlobalSign. Saiba como [integrar um CA de confiança com o cofre de chaves.](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)

## <a name="renewal-of-non-integrated-ca-certificate"></a>Renovação do certificado de CA não integrado 
O cofre-chave Azure proporciona aos seus utilizadores o benefício de importar certificados de qualquer AC para permitir que os seus utilizadores se integrem com vários recursos Azure e facilitem a implementação. Se está preocupado em perder a pista do seu certificado caducado, ou pior, descobriu que o seu certificado já expirou; então Key Vault pode ajudá-lo a manter-se atualizado. Para o certificado de CA não integrado, o cofre-chave permite ao seu utilizador configurar notificações de e-mail de quase expiração. Estas notificações também podem ser definidas para vários utilizadores.

Agora, para renovar um certificado, é importante entender que um certificado Azure Key Vault é um objeto versado. Se a versão atual estiver a expirar, terá de criar uma nova versão. Conceptualmente, cada nova versão seria um novo certificado composto por chave e uma bolha que liga essa chave a uma identidade. Quando utilizar um CA não parceiro, o cofre de chaves gerará um par de valores chave e devolverá a RSE.

**Passos a seguir no portal Azure:-**
1.  Abra o certificado que pretende renovar.
2.  Selecione **+ botão versão nova** no ecrã do certificado.
3.  Selecione **operação de certificado**
4.  **Selecione Download CSR**. Isto irá descarregar um ficheiro .csr na sua unidade local.
5.  Traga a CSR à sua escolha de CA para assinar o pedido
6.  Traga de volta o pedido assinado e selecione **Merge CSR** no mesmo ecrã de operação certificado.

> [!NOTE]
> É importante fundir a RSE assinada com o mesmo pedido de RSE que foi criado, caso contrário a chave não corresponderia.

Os passos são semelhantes à criação de um novo certificado e são documentados mais em detalhes [aqui.]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)

## <a name="renewal-of-self-signed-certificate"></a>Renovação do certificado auto-assinado

Boas notícias de novo! A Azure Key Vaults também cuidará da renovação automática de certificados auto-assinados para os seus utilizadores. Para saber mais sobre a alteração da política de emissão e a atualização dos atributos de ação vitalícia do certificado, leia mais [aqui.](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)

### <a name="troubleshoot"></a>Resolução de problemas
Se o certificado emitido estiver em estado de 'desactivado' no portal Azure, consulte a Operação certificado para visualizar a mensagem de erro desse certificado.

### <a name="frequently-asked-questions"></a>Perguntas frequentes
* Como posso testar a característica de autorotação do certificado?
  Crie um certificado com validade de 1 mês e, em seguida, desemalte a ação de tempo de vida para rotação em 1%. Esta definição rodará o certificado em 7,2 horas.
  
* As etiquetas serão replicadas após a renovação automática do certificado?
  Sim, as etiquetas seriam replicadas após a renovação automática.

### <a name="see-also"></a>Consulte também
*   [Integrar o Key Vault com a Autoridade de Certificação DigiCert](how-to-integrate-certificate-authority.md)
*   [Tutorial: Configurar o certificado autorrotação no Cofre de Chaves](tutorial-rotate-certificates.md)
