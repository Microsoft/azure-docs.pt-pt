---
title: Começar - Ferramenta de Modelação de Ameaças da Microsoft - Azure / Microsoft Docs
description: Saiba como começar a usar a Ferramenta de Modelação de Ameaças. Criar um diagrama, identificar ameaças, mitigar ameaças e validar cada atenuação.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: eb0aff6692a12ca7fb00090c0585e46f37c84ace
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913029"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Começar com a ferramenta de modelação de ameaças

A Ferramenta de Modelação de Ameaças da Microsoft 2018 foi lançada como GA em setembro de 2018 como um **[click-to-download](https://aka.ms/threatmodelingtool)** gratuito . A alteração do mecanismo de entrega permite-nos impulsionar as mais recentes melhorias e correções de bugs para os clientes sempre que abrem a ferramenta, facilitando a manutenção e utilização.
Este artigo leva-o ao longo do processo de começar com a abordagem de modelação de ameaças da Microsoft SDL e mostra-lhe como usar a ferramenta para desenvolver modelos de grande ameaça como uma espinha dorsal do seu processo de segurança.

Este artigo baseia-se no conhecimento existente da abordagem de modelação de ameaças SDL. Para uma revisão rápida, consulte as **[aplicações web de modelação de ameaças](https://msdn.microsoft.com/library/ms978516.aspx)** e uma versão arquivada de Falhas de Segurança Uncover Usando o artigo **[MSDN de abordagem STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** publicado em 2006.

Para resumir rapidamente, a abordagem consiste em criar um diagrama, identificar ameaças, atenuá-las e validar cada mitigação. Aqui está um diagrama que destaca este processo:

![Processo SDL](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Iniciar o processo de modelação de ameaças

Quando lançar a Ferramenta de Modelação de Ameaças, vai notar algumas coisas, como se pode ver na imagem:

![Página de início em branco](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Secção de modelo de ameaça

| Componente                                   | Detalhes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Botão de feedback, sugestões e problemas** | Leva-te o **[Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** para todas as coisas SDL. Dá-lhe a oportunidade de ler o que outros utilizadores estão a fazer, juntamente com soluções alternativas e recomendações. Se ainda não consegue encontrar o que procura, envie um e-mail tmtextsupport@microsoft.com para a nossa equipa de apoio para o ajudar.                                                                                                                            |
| **Criar um Modelo**                          | Abre uma tela em branco para desenhar o seu diagrama. Certifique-se de selecionar qual o modelo que gostaria de usar para o seu modelo                                                                                                                                                                                                                                                                                                                                                                       |
| **Modelo para novos modelos**                 | Deve selecionar qual o modelo a utilizar antes de criar um modelo. O nosso modelo principal é o Modelo de Modelo de Ameaça Azure, que contém stencils, ameaças e mitigações específicos do Azure. Para modelos genéricos, selecione a Base de Conhecimento SDL TM do menu suspenso. Pretende criar o seu próprio modelo ou submeter um novo para todos os utilizadores? Confira a nossa **[página gitHub do repositório de modelos](https://github.com/Microsoft/threat-modeling-templates)** para saber mais                              |
| **Abrir um modelo**                            | <p>Abre modelos de ameaça previamente guardados. A funcionalidade Modelos Recentemente Abertos é ótima se precisar de abrir os seus ficheiros mais recentes. Quando pairar sobre a seleção, encontrará duas formas de abrir modelos:</p><p><ul><li>Abra a partir deste computador – forma clássica de abrir um ficheiro usando armazenamento local</li><li>Open from OneDrive – as equipas podem usar pastas no OneDrive para economizar e partilhar todos os seus modelos de ameaça num único local para ajudar a aumentar a produtividade e a colaboração</li></ul></p> |
| **Guia de início**                   | Abre a página principal da **[Ferramenta de Modelação de Ameaças da Microsoft](threat-modeling-tool.md)**                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Seção de modelo

| Componente               | Detalhes                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Criar novo modelo** | Abre um modelo em branco para construir. A menos que tenha um vasto conhecimento em modelos de construção do zero, recomendamos que construa a partir dos existentes |
| **Modelo aberto**       | Abre os modelos existentes para que possa fazer alterações                                                                                                              |

A equipa da Ferramenta de Modelação de Ameaças está constantemente a trabalhar para melhorar a funcionalidade e experiência da ferramenta. Algumas pequenas mudanças podem ocorrer ao longo do ano, mas todas as mudanças importantes requerem reescritas no guia. Consulte-o frequentemente para garantir que obtém os últimos anúncios.

## <a name="building-a-model"></a>Construção de um modelo

Nesta secção, seguimos:

- Cristina (uma desenvolvedora)
- Ricardo (gestor de programas) e
- Ashish (um teste)

Estão a passar pelo processo de desenvolvimento do seu primeiro modelo de ameaça.

> Olá Cristina, trabalhei no diagrama do modelo de ameaça e queria ter a certeza que tínhamos os detalhes certos. Pode ajudar-me a ver?
> Cristina: Absolutamente. Vamos dar uma vista de olhos.
> Ricardo abre a ferramenta e partilha o ecrã com Cristina.

![Modelo de ameaça básica](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Ok, parece simples, mas podes passar por isso?
> Ricardo: Claro! Aqui está a avaria:
> - Nosso utilizador humano é desenhado como uma entidade externa - um quadrado
> - Estão a enviar comandos para o nosso servidor Web - o círculo
> - O servidor Web está a consultar uma base de dados (duas linhas paralelas)

O que o Ricardo acabou de mostrar à Cristina é um DFD, abreviatura **[de Data Flow Diagram.](https://en.wikipedia.org/wiki/Data_flow_diagram)** A Ferramenta de Modelação de Ameaças permite que os utilizadores especifiquem limites de confiança, indicados pelas linhas pontilhadas vermelhas, para mostrar onde diferentes entidades estão no controlo. Por exemplo, os administradores de TI requerem um sistema de Diretório Ativo para fins de autenticação, pelo que o Diretório Ativo está fora do seu controlo.

> Parece-me bem. E as ameaças?
> Deixe-me mostrar-lhe.

## <a name="analyzing-threats"></a>Analisar ameaças

Assim que clica na visão de análise da seleção do menu do ícone (ficheiro com lupa), ele é levado para uma lista de ameaças geradas a Ferramenta de Modelação de Ameaças encontrada com base no modelo padrão, que utiliza a abordagem SDL chamada **[STRIDE (Spoofing, Tampering, Info Disclosure, Repudiation, Denial of Service and Elevation of Privilege)](https://en.wikipedia.org/wiki/STRIDE_(security))** . A ideia é que o software esteja sob um conjunto previsível de ameaças, que podem ser encontradas usando estas 6 categorias.

Esta abordagem é como proteger a sua casa garantindo que cada porta e janela tem um mecanismo de bloqueio no lugar antes de adicionar um sistema de alarme ou perseguir o ladrão.

![Ameaças Básicas](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo começa por selecionar o primeiro item da lista. Eis o que acontece:

Primeiro, a interação entre os dois stencils é melhorada

![A screenshot mostra dois stencils e a seta curva ligando-os num peso mais pesado da linha.](./media/threat-modeling-tool-getting-started/interaction.png)

Em segundo lugar, informações adicionais sobre a ameaça aparecem na janela Propriedades de Ameaça

![A screenshot mostra a janela Propriedades de Ameaça, que contém Título, Categoria, Descrição, Interação e Prioridade.](./media/threat-modeling-tool-getting-started/interactioninfo.png)

A ameaça gerada ajuda-o a compreender potenciais falhas de design. A categorização do STRIDE dá-lhe uma ideia sobre potenciais vetores de ataque, enquanto a descrição adicional diz-lhe exatamente o que está errado, juntamente com possíveis formas de mitigar. Ele pode usar campos editáveis para escrever notas nos detalhes da justificação ou alterar classificações prioritárias dependendo da barra de erros da sua organização.

Os modelos Azure têm detalhes adicionais para ajudar os utilizadores a entender não só o que está errado, mas também como corrigi-lo adicionando descrições, exemplos e hiperligações à documentação específica do Azure.

A descrição fê-lo perceber a importância de adicionar um mecanismo de autenticação para evitar que os utilizadores sejam falsificados, revelando a primeira ameaça a ser trabalhada. A poucos minutos do debate com Cristina, entenderam a importância de implementar o controlo de acessos e papéis. Ricardo preencheu algumas notas rápidas para garantir que estas foram implementadas.

À medida que Ricardo entrava nas ameaças no âmbito da Divulgação de Informação, percebeu que o plano de controlo de acesso exigia algumas contas só de leitura para auditoria e geração de relatórios. Ele perguntou-se se esta deveria ser uma nova ameaça, mas as mitigações eram as mesmas, por isso observou a ameaça em conformidade.
Ele também pensou na divulgação de informação um pouco mais e percebeu que as fitas de reserva iam precisar de encriptação, um trabalho para a equipa de operações.

As ameaças não aplicáveis ao desenho devido a mitigações ou garantias de segurança existentes podem ser alteradas para "Não aplicável" a partir da queda do Estado. Existem três outras opções: Não iniciada – seleção padrão, Investigação de Necessidades – usada para acompanhar itens e Mitigação – uma vez que está totalmente trabalhada.

## <a name="reports--sharing"></a>Relatórios & partilha

Assim que Ricardo analisar a lista com Cristina e adicionar notas importantes, mitigações/justificações, alterações de prioridade e de estado, seleciona Relatórios -> Criar Relatório Completo -> Save Report, que publica um relatório agradável para que ele vá em frente com os colegas para garantir que o trabalho de segurança adequado é implementado.

![A screenshot mostra um relatório representativo de modelação de ameaças.](./media/threat-modeling-tool-feature-overview/report.png)

Se o Ricardo quiser partilhar o ficheiro, pode facilmente fazê-lo economizando na conta OneDrive da sua organização. Assim que o fizer, pode copiar o link do documento e partilhá-lo com os seus colegas. 

## <a name="threat-modeling-meetings"></a>Reuniões de modelação de ameaças

Quando Ricardo enviou o seu modelo de ameaça ao seu colega usando o OneDrive, Ashish, o tester, ficou subjugado. Parecia que Ricardo e Cristina falharam alguns casos importantes de canto, que poderiam ser facilmente comprometidos. O seu ceticismo é um complemento a modelos de ameaças.

Neste cenário, depois de Ashish ter assumido o modelo de ameaça, apelou a duas reuniões de modelo de ameaça: uma reunião para sincronizar o processo e caminhar pelos diagramas e depois uma segunda reunião para revisão de ameaças e aprovação.

No primeiro encontro, Ashish passou 10 minutos a passear toda a gente através do processo de modelação de ameaças SDL. Em seguida, puxou o diagrama do modelo de ameaça e começou a explicá-lo em detalhe. Em cinco minutos, um importante componente desaparecido tinha sido identificado.

Poucos minutos depois, Ashish e Ricardo entraram numa discussão alargada sobre como o servidor web foi construído. Não era a forma ideal de uma reunião prosseguir, mas todos acabaram por concordar que descobrir a discrepância mais cedo lhes pouparia tempo no futuro.

No segundo encontro, a equipa atravessou as ameaças, discutiu algumas formas de as abordar e assinou o modelo de ameaça. Verificaram o documento em controlo de fontes e continuaram com o desenvolvimento.

## <a name="thinking-about-assets"></a>Pensar em ativos

Alguns leitores que têm uma ameaça modelada podem notar que não falamos de bens. Descobrimos que muitos engenheiros de software entendem melhor o seu software do que entendem o conceito de ativos e quais os ativos que um intruso pode estar interessado.

Se vai ameaçar modelar uma casa, pode começar por pensar na sua família, em fotos insubstituíveis ou em obras de arte valiosas. Talvez comeces por pensar em quem pode entrar e no atual sistema de segurança. Ou pode começar por considerar as características físicas, como a piscina ou a varanda da frente. Estes são análogos a pensar em ativos, atacantes ou design de software. Qualquer uma destas três abordagens funciona.

A abordagem à modelação de ameaças que apresentámos aqui é substancialmente mais simples do que o que a Microsoft fez no passado. Descobrimos que a abordagem de design de software funciona bem para muitas equipas. Esperamos que isso inclua o seu.

## <a name="next-steps"></a>Passos Seguintes

Envie as suas perguntas, comentários e preocupações tmtextsupport@microsoft.com para. **[Descarregue](https://aka.ms/threatmodelingtool)** a Ferramenta de Modelação de Ameaças para começar.
