---
title: Não é possível adicionar domínio personalizado usando o certificado Key Vault
titleSuffix: Azure API Management
description: Aprenda a resolver o problema em que não pode adicionar um domínio personalizado na Azure API Management usando um certificado de cofre chave.
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430582"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>Falha na atualização dos nomes de anfitriões do serviço de gestão da API

Este artigo descreve o erro "Falhaem em atualizar os nomes de anfitriões do serviço api Management" que poderá experimentar quando adicionar um domínio personalizado para o serviço de Gestão API Azure. Este artigo fornece medidas de resolução de problemas para ajudá-lo a resolver o problema.

## <a name="symptoms"></a>Sintomas

Quando tenta adicionar um domínio personalizado para o seu serviço de Gestão API utilizando um certificado do Cofre de Chaves Azure, recebe a seguinte mensagem de erro:

- Falhou em atualizar os nomes de anfitriões do serviço api Management. Pedido dehttps://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0recurso ' falhou com o StatusCode: Proibido para RequestId: . Mensagem de exceção: A operação devolveu um código de estado inválido 'Proibido'.

## <a name="cause"></a>Causa

O serviço de Gestão API não tem permissão para aceder ao cofre chave que está a tentar utilizar para o domínio personalizado.

## <a name="solution"></a>Solução

Para resolver este problema, siga estes passos:

1. Vá ao [portal Azure,](Https://portal.azure.com)selecione a sua instância de Gestão API e, em seguida, selecione **identidades geridas**. Certifique-se de que o Registo com a opção **Diretório Ativo Azure** está definido para **Sim**. 
    ![Registro-se com O Diretor Ativo azure](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png)
1. No portal Azure, abra o serviço **de cofres Key** e selecione o cofre chave que está a tentar usar para o domínio personalizado.
1. Selecione **as políticas**de Acesso e verifique se existe um diretor de serviço que corresponda ao nome da instância de serviço de Gestão API. Se houver, selecione o diretor de serviço e certifique-se de que tem a permissão **Get** listada sob **permissões secretas**.  
    ![Adicionar política de acesso ao principal de serviço](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png)
1. Se o serviço de Gestão API não estiver na lista, selecione Adicionar a política de **acesso,** e, em seguida, criar a seguinte política de acesso:
    - **Configurar a partir do modelo:** Nenhum
    - **Selecione principal:** Procure o nome do serviço de Gestão API e, em seguida, selecione-o a partir da lista
    - **Principais permissões**: Nenhuma
    - **Permissões secretas**: Obter
    - **Permissões de certificado**: Nenhuma
1. Selecione **OK** para criar a política de acesso.
1. Selecione **Guardar** para guardar as alterações.

Verifique se o problema está resolvido. Para isso, tente criar o domínio personalizado no serviço de Gestão API utilizando o certificado Key Vault.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o serviço de Gestão API:

- Confira mais [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre a Gestão da API.
* Para outras formas de garantir o seu serviço de back-end, consulte a [autenticação do Certificado Mútuo](api-management-howto-mutual-certificates.md).

* Criar uma instância de [serviço de Gestão API.](get-started-create-service-instance.md)
* [Gerencie a sua primeira API.](import-and-publish.md)