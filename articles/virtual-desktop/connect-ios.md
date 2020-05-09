---
title: Ligue ao Windows Virtual Desktop a partir do iOS - Azure
description: Como ligar ao Windows Virtual Desktop utilizando o cliente iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4239f5271e4df282cf667b4bf1eff5f55f6f6323
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611709"
---
# <a name="connect-with-the-ios-client"></a>Ligar ao cliente de iOS

> Aplica-se a: iOS 13.0 ou mais tarde. Compatível com iPhone, iPad e iPod touch.

>[!IMPORTANT]
>Este conteúdo aplica-se à atualização da primavera de 2020 com os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows. Se estiver a utilizar o lançamento do Windows Virtual Desktop Fall 2019 sem objetos do Gestor de Recursos Azure, consulte [este artigo](./virtual-desktop-fall-2019/connect-ios-2019.md).
>
> A atualização Do Windows Virtual Desktop Spring 2020 encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
> Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Pode aceder aos recursos do Windows Virtual Desktop a partir do seu dispositivo iOS com o nosso cliente descarregado. Este guia dir-lhe-á como configurar o cliente iOS.

## <a name="install-the-ios-client"></a>Instale o cliente iOS

Para começar, [faça o download](https://aka.ms/rdios) e instale o cliente no seu dispositivo iOS.

## <a name="subscribe-to-a-feed"></a>Subscreva um feed

Subscreva o feed fornecido pelo seu administrador para obter a lista de recursos geridos a que pode aceder no seu dispositivo iOS.

Para subscrever um feed:

1. No Centro de **+** Ligação, toque , toque em **Adicionar Espaço de Trabalho**.
2. Introduza o URL de alimentação no campo **URL** feed. O URL de alimentação pode ser um URL ou um endereço de e-mail.
   - Se usar um URL, use o que o seu administrador lhe deu. Normalmente, o <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>URL é .
   - Para utilizar o seu email, insira o seu endereço de e-mail. Isto diz ao cliente para procurar um URL associado ao seu endereço de e-mail se o seu administrador configurar o servidor dessa forma.
3. Toque **em Seguida**.
4. Forneça as suas credenciais quando solicitado.
   - Para **o nome do utilizador,** dê ao utilizador o nome do utilizador com permissão para aceder aos recursos.
   - Para **palavra-passe,** forneça a palavra-passe associada ao nome do utilizador.
   - Também pode ser solicitado a fornecer fatores adicionais se a sua administração configurar a autenticação dessa forma.
5. Toque em **Guardar**.

Depois disso, o Centro de Ligação deve apresentar os recursos remotos.

Uma vez subscrito um feed, o conteúdo do feed irá atualizar-se automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo seu administrador.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como usar o cliente iOS, confira o Get started com a documentação [do cliente iOS.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/)
