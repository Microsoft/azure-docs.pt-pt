---
title: Sobre a renovação do certificado Azure Key Vault
description: Este artigo discute como renovar os certificados Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3809fa9e1ce17a5a0c3cf333ac20ef543db4b5a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588808"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Renove os seus certificados Azure Key Vault

Com o Azure Key Vault, pode facilmente provisá-lo, gerir e implementar certificados digitais para a sua rede e permitir comunicações seguras para as suas aplicações. Para obter mais informações sobre certificados, consulte [os certificados Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates).

Ao utilizar certificados de curta duração ou aumentando a frequência de rotação do certificado, pode ajudar a impedir o acesso às suas aplicações por utilizadores não autorizados.

Este artigo discute como renovar os seus certificados Azure Key Vault.

## <a name="get-notified-about-certificate-expirations"></a>Ser notificado sobre expirações de certificados
Para ser notificado quando os seus certificados estiverem prestes a expirar, faça o seguinte:

Em primeiro lugar, adicione um contacto de certificado ao seu cofre de chaves utilizando o cmdlet PowerShell [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0).

Segundo, configuure-se quando pretender ser notificado sobre a expiração do certificado. Para configurar os atributos do ciclo de vida do certificado, consulte a [autorotação do certificado de configuração no Cofre-Chave](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

No Cofre-Chave, existem três categorias de certificados:
-   Certificados criados com uma autoridade integrada de certificados (CA), tais como DigiCert ou GlobalSign
-   Certificados que são criados com uma AC não-agred
-   Certificados auto-assinados

## <a name="renew-an-integrated-ca-certificate"></a>Renovar um certificado de CA integrado 
O Azure Key Vault trata da manutenção de certificados de ponta a ponta que são emitidos pelas autoridades de certificados da Microsoft DigiCert e GlobalSign. Saiba como [integrar um CA de confiança com o Key Vault.](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)

## <a name="renew-a-nonintegrated-ca-certificate"></a>Renovar um certificado de CA não-antegrado 
Ao utilizar o Azure Key Vault, pode importar certificados de qualquer CA, um benefício que lhe permite integrar-se com vários recursos Azure e facilitar a implementação. Se está preocupado em perder o rasto das datas de validade do certificado ou, pior, descobriu que um certificado já expirou, o seu cofre-chave pode ajudá-lo a manter-se atualizado. Para certificados de CA não não agritos, o cofre-chave permite configurar notificações de correio eletrónico de quase validade. Estas notificações também podem ser definidas para vários utilizadores.

> [!IMPORTANT]
> Um certificado é um objeto ver versão. Se a versão atual estiver a expirar, terá de criar uma nova versão. Conceptualmente, cada nova versão é um novo certificado que é composto por uma chave e uma bolha que liga essa chave a uma identidade. Quando utiliza um CA não participante, o cofre gera um par chave/valor e devolve um pedido de assinatura de certificado (CSR).

Para renovar um certificado de CA não-antegrado, faça o seguinte:

1. Inscreva-se no portal Azure e, em seguida, abra o certificado que pretende renovar.
1. No painel de certificados, selecione **Nova Versão**.
1. Selecione **Operação certificado**.
1. Selecione **Baixar CSR** para descarregar um ficheiro CSR para a sua unidade local.
1. Envie a RSE à sua escolha de CA para assinar o pedido.
1. Traga de volta o pedido assinado e selecione **Merge CSR** no mesmo painel de operação de certificado.

> [!NOTE]
> É importante fundir a RSE assinada com o mesmo pedido de RSE que criou. Caso contrário, a chave não combina.

Para obter mais informações sobre a criação de uma nova RSE, consulte [Criar e fundir uma RSE no Key Vault]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Renovar um certificado auto-assinado

Azure Key Vault também lida com a autoestima de certificados auto-assinados. Para saber mais sobre a alteração da política de emissão e a atualização dos atributos do ciclo de vida de um certificado, consulte a autoração do [certificado de configuração no Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Resolução de problemas
Se o certificado emitido estiver em estado *de desativação* no portal Azure, vá à **Operação Certificado** para ver a mensagem de erro do certificado.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Como posso testar a característica de autorotação do certificado?**

Crie um certificado com uma validade de **1 mês,** e, em seguida, desemote a ação vitalícia para rotação em **1%.** Esta definição rodará o certificado a cada 7,2 horas.
  
**As etiquetas serão replicadas após a atribuição do certificado?**

Sim, as etiquetas são replicadas após a autoestima.

## <a name="next-steps"></a>Passos seguintes
*   [Integrar cofre-chave com autoridade de certificados DigiCert](how-to-integrate-certificate-authority.md)
*   [Tutorial: Configure a autoração do certificado no Cofre-Chave](tutorial-rotate-certificates.md)
