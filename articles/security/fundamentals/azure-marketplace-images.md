---
title: Recomendações de Segurança para Imagens do Mercado Azure [ Microsoft Docs
description: Este artigo fornece recomendações para imagens incluídas no mercado
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: terrylan
ms.openlocfilehash: 3925e39824d1702ff43a6b981ac997ddab658b96
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548661"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendações de segurança para imagens do mercado azure

A sua imagem deve cumprir estas recomendações de configuração de segurança. Isto ajuda a manter um alto nível de segurança para imagens de soluções de parceiros no Mercado Azure.

Faça sempre uma deteção de vulnerabilidade de segurança na sua imagem antes de ser submetida. Se detetar uma vulnerabilidade de segurança na sua própria imagem publicada, deve informar os seus clientes atempadamente da vulnerabilidade e de como corrigi-la.

## <a name="open-source-based-images"></a>Imagens baseadas em Código aberto

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoria**                                                 | **Verifique**                                                                                                                                                                                                                                                                              |
| Segurança                                                     | Instale todos os mais recentes patches de segurança para a distribuição linux.                                                                                                                                                                                                              |
| Segurança                                                     | Siga as diretrizes da indústria para garantir a imagem VM para a distribuição específica do Linux.                                                                                                                                                                                     |
| Segurança                                                     | Limite a superfície de ataque mantendo a pegada mínima com apenas as funções, funcionalidades, serviços e portas de rede necessárias do Windows Server.                                                                                                                                               |
| Segurança                                                     | Scaneie código fonte e imagem VM resultante para malware.                                                                                                                                                                                                                                   |
| Segurança                                                     | A imagem VHD inclui apenas contas trancadas necessárias que não possuam senhas padrão que permitam o login interativo; sem portas traseiras.                                                                                                                                           |
| Segurança                                                     | Desative as regras de firewall a menos que a aplicação dependa funcionalmente delas, como um aparelho de firewall.                                                                                                                                                                             |
| Segurança                                                     | Remova todas as informações sensíveis da imagem VHD, tais como chaves SSH de teste, ficheiros de anfitriões conhecidos, ficheiros de registo e certificados desnecessários.                                                                                                                                       |
| Segurança                                                     | Evite utilizar o LVM.                                                                                                                                                                                                                                            |
| Segurança                                                     | Inclua as versões mais recentes das bibliotecas necessárias: </br> - OpenSSL v1.0 ou superior </br> - Python 2.5 ou superior (Python 2.6+ é altamente recomendado) </br> - Pacote Python pyasn1 se ainda não estiver instalado </br> - d.OpenSSL v 1.0 ou superior                                                                |
| Segurança                                                     | Entradas claras de história bash/shell.                                                                                                                                                                                                                                             |
| Redes                                                   | Inclua o servidor SSH por padrão. Defina o SSH manter-se vivo para sshd config com a seguinte opção: ClientAliveInterval 180.                                                                                                                                                        |
| Redes                                                   | Remova qualquer configuração de rede personalizada da imagem. Eliminar o resolve.conf: `rm /etc/resolv.conf`.                                                                                                                                                                                |
| Implementação                                                   | Instale o mais recente Agente Azure Linux.</br> - Instale utilizando o pacote RPM ou Deb.  </br> - Também pode utilizar o processo de instalação manual, mas as embalagens do instalador são recomendadas e preferidas. </br> - Se instalar manualmente o agente a partir do repositório GitHub, copie primeiro o `waagent` ficheiro para `/usr/sbin` e executar (como raiz): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>O ficheiro de configuração do agente é colocado em `/etc/waagent.conf`. |
| Implementação                                                   | Certifique-se de que o Suporte Azure pode fornecer aos nossos parceiros a saída de consola em série quando necessário e fornecer um tempo limite adequado para a montagem do disco OS a partir do armazenamento em nuvem. Adicione os seguintes parâmetros à imagem `console=ttyS0 earlyprintk=ttyS0 rootdelay=300`Kernel Boot Line: . |
| Implementação                                                   | Sem troca de divisória no disco OS. A troca pode ser solicitada para criação no disco de recursos locais pelo Agente Linux.         |
| Implementação                                                   | Crie uma única partição de raiz para o disco OS.      |
| Implementação                                                   | Apenas sistema operativo de 64 bits.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imagens baseadas em Servidor esparto

|||
|-------------| -------------------------|
| **Categoria**                                                     | **Verifique**                                                                                                                                                                |
| Segurança                                                         | Utilize uma imagem segura da base do OS. O VHD utilizado para a origem de qualquer imagem baseada no Windows Server deve ser a partir das imagens DO OS do Windows Server fornecidas através do Microsoft Azure. |
| Segurança                                                         | Instale todas as atualizações de segurança mais recentes.                                                                                                                                     |
| Segurança                                                         | As aplicações não devem depender de nomes de utilizadores restritos, como administrador, raiz ou administrador.                                                                |
| Segurança                                                         | Ativar a encriptação de unidade BitLocker para discos rígidos osso e discos rígidos de dados.                                                             |
| Segurança                                                         | Limite a superfície de ataque mantendo a pegada mínima com apenas as funções, funcionalidades, serviços e portas de rede necessárias do Windows Server.                         |
| Segurança                                                         | Scaneie código fonte e imagem VM resultante para malware.                                                                                                                     |
| Segurança                                                         | Defina a atualização de segurança das imagens do Windows Server para a atualização automática.                                                                                                                |
| Segurança                                                         | A imagem VHD inclui apenas contas trancadas necessárias que não possuam senhas padrão que permitam o login interativo; sem portas traseiras.                             |
| Segurança                                                         | Desative as regras de firewall a menos que a aplicação dependa funcionalmente delas, como um aparelho de firewall.                                                               |
| Segurança                                                         | Remova todas as informações sensíveis da imagem VHD, incluindo ficheiros HOSTS, ficheiros de registo e certificados desnecessários.                                              |
| Implementação                                                       | Apenas sistema operativo de 64 bits.                            |

Mesmo que a sua organização não tenha imagens no mercado Azure, considere verificar as configurações de imagem do Windows e do Linux contra estas recomendações.

## <a name="contacting-customers"></a>Contato com clientes

Para identificar os clientes e os seus e-mails de contacto:

1.  No Cloud Partner Portal, no trilho esquerdo, selecione **Insights**.
2.  No separador **Encomendas e Utilização,** utilize os campos de data de **início** e data de **fim** para consultar a utilização dentro do intervalo de data exigido. Isto mostra quais as subscrições do Azure utilizadas diariamente para a oferta. Exporte estes dados. 
3.  Da mesma forma, no separador **Cliente,** consulta e exportação da sua base de clientes.
4.  Combine o ID de subscrição do passo 2 para o ID de subscrição do passo 3 para encontrar as informações necessárias ao cliente.
