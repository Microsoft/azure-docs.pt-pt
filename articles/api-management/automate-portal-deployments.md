---
title: Automatizar implementações do portal do desenvolvedor
titleSuffix: Azure API Management
description: Saiba como migrar automaticamente o conteúdo do portal de desenvolvedores auto-hospedado entre dois serviços de Gestão API.
author: erikadoyle
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e189a9339f6ca3bc81148b86206ddd052392074f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741921"
---
# <a name="automate-developer-portal-deployments"></a>Automatizar implementações do portal do desenvolvedor

O portal de desenvolvimento da API Management suporta o acesso programático aos conteúdos. Permite importar dados ou exportar de um serviço de Gestão de API através da gestão de [conteúdos REST API.](/rest/api/apimanagement/) O acesso da API REST funciona tanto para portais geridos como para portais auto-hospedados.

## <a name="automated-migration-script"></a>Roteiro de migração automatizado

Pode utilizar a API para automatizar a migração de conteúdos entre dois serviços de Gestão da API - por exemplo, um serviço no ambiente de teste e um serviço no ambiente de produção. O `scripts.v3/migrate.js` script no portal de desenvolvimento da API Management [GitHub simplifica](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js) este processo de automação.

> [!WARNING]
> O script remove os conteúdos do portal de desenvolvedores no seu serviço de gestão API de destino. Se estás preocupado com isso, certifica-te de que fazes uma cópia de segurança.

> [!NOTE]
> Se estiver a utilizar um portal auto-hospedado com uma conta de armazenamento personalizada explicitamente definida para hospedar ficheiros de mídia (ou seja, define a `blobStorageUrl` definição no `config.design.json` ficheiro de configuração), tem de utilizar o script original `scripts/migrate.js` [](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js). O script original não funciona para portais geridos ou auto-hospedados com a conta de armazenamento de mídia gerida pela API Management. Nesse caso, utilize o script da `/scripts.v3` pasta.

O script realiza os passos seguintes:

1. Capture o conteúdo do portal e os meios de comunicação a partir do serviço de Gestão API de origem.
1. Remova o conteúdo do portal e os meios de comunicação do serviço de gestão API de destino.
1. Faça o upload do conteúdo do portal e dos meios de comunicação para o serviço de gestão de destino API.
1. Opcionalmente e apenas para portais geridos - publique automaticamente o portal.

Após a execução do script com sucesso, o serviço de Gestão API-alvo deverá conter o mesmo conteúdo do portal que o serviço de origem e poderá vê-lo como um administrador.

* Se estiver a utilizar um portal gerido, pode definir o script para publicar automaticamente o portal de destino para disponibilizar automaticamente a versão migratória aos visitantes. 
* Se estiver a utilizar um portal auto-hospedado, tem de publicar manualmente o portal de destino. Siga as instruções de publicação e hospedagem no tutorial para [criar um portal de desenvolvedores auto-hospedado](developer-portal-self-host.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o portal de desenvolvedores:

- [Descrição geral do portal do programador da Gestão de API do Azure](api-management-howto-developer-portal.md)
- [Auto-anfitrião do portal de desenvolvedores](developer-portal-self-host.md)