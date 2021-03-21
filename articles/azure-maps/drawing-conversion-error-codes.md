---
title: Azure Maps Erros e avisos de conversão de desenho
description: Saiba mais sobre os erros e avisos de Conversão que poderá encontrar enquanto estiver a utilizar o serviço de Conversão Azure Maps. Leia as recomendações sobre como resolver os erros e os avisos, com alguns exemplos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: cecc19f0984ce1801d50e5cbda73e98a01e2825b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906221"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Erros de conversão de desenho e avisos

O [serviço de Conversão Azure Maps](/rest/api/maps/conversion) permite converter pacotes de desenho carregados em dados de mapas. As embalagens de desenho devem respeitar os [requisitos](drawing-requirements.md)do pacote de desenho . Se um ou mais requisitos não forem cumpridos, o serviço de Conversão devolverá erros ou avisos. Este artigo lista os códigos de erro de conversão e de aviso, com recomendações sobre como resolvê-los. Também fornece alguns exemplos de desenhos que podem fazer com que o serviço de Conversão devolva estes códigos.

O serviço de Conversão terá sucesso se houver avisos de conversão. No entanto, recomenda-se que reveja e resolva todos os avisos. Um aviso significa que parte da conversão foi ignorada ou fixada automaticamente. Não resolver os avisos pode resultar em erros nestes últimos processos.

## <a name="general-warnings"></a>Avisos Gerais

### <a name="geometrywarning"></a>**geometria Aavisar**

#### <a name="description-for-geometrywarning"></a>*Descrição para geometriaAavião*

Uma **geometria O aviso de aviso** ocorre quando o desenho contém uma entidade inválida. Uma entidade inválida é uma entidade que não se conforma com restrições geométricas. Exemplos de uma entidade inválida são um polígono auto-interseccionado ou um PolyLine não fechado numa camada que suporta apenas a geometria fechada.

O serviço De Conversão não é capaz de criar uma funcionalidade de mapa a partir de uma entidade inválida e, em vez disso, ignora-o.

#### <a name="examples-for-geometrywarning"></a>*Exemplos para geometriaAvining*

* As duas imagens abaixo mostram exemplos de polígonos auto-interseccionados.

     ![Exemplo de um polígono auto-interseccionado, exemplo um.](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Exemplo de um polígono auto-interseccionado, exemplo dois.](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Abaixo está uma imagem que mostra um PolyLine não fechado. Suponha que a camada só suporta geometria fechada.

    ![Exemplo de uma Linha Poliline não fechada](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*Como corrigir geometriaAAvisão*

Inspecione a **geometria Aavisar** cada entidade para verificar se segue restrições geométricas.

### <a name="unexpectedgeometryinlayer"></a>**inesperadoGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*Descrição para InesperadoGeometryInLayer*

Um aviso **inesperado deGeometryInLayer** ocorre quando o desenho contém geometria que é incompatível com o tipo de geometria esperado para uma determinada camada. Quando o serviço de Conversão retornar um aviso **inesperado deGeometryInLayer,** ignorará essa geometria.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Exemplo para inesperadoGeometryInLayer*

A imagem abaixo mostra um PolyLine não fechado. Suponha que a camada só suporta geometria fechada.

![Exemplo de uma Linha Poliline não fechada](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*Como corrigir o inesperadoGeometryInLayer*

Inspecione cada aviso **inesperado deGeometryInLayer** e mova a geometria incompatível para uma camada compatível. Se não for compatível com nenhuma das outras camadas, deve ser removido.

### <a name="unsupportedfeaturerepresentation"></a>**não suportadoFeatureRepresentação**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*Descrição para não suportadoPropresentação*

O aviso de apresentação não **suportado ocorre** quando o desenho contém um tipo de entidade não suportada.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Exemplo para não suportadoPropresentação*

A imagem abaixo mostra um tipo de entidade não suportada como um objeto de texto multi-linha numa camada de etiqueta.
  
![Exemplo de um objeto de texto multi-linha na camada do rótulo](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*Como corrigir a apresentação não suportada*

Certifique-se de que os seus ficheiros DWG contêm apenas os tipos de entidades suportadas. Os tipos suportados estão listados na [secção de requisitos de ficheiros de desenho no artigo de requisitos do pacote de desenho](drawing-requirements.md#drawing-package-requirements).

### <a name="automaticrepairperformed"></a>**automáticoRepairPerforme**

#### <a name="description-for-automaticrepairperformed"></a>*Descrição para automáticoRepairPerformed*

O aviso **automáticoRepairPerformed** ocorre quando o serviço de Conversão repara automaticamente geometria inválida.

#### <a name="examples-for-automaticrepairperformed"></a>*Exemplos para AutomáticaRepairPerformed*

* A imagem a seguir mostra como o serviço de Conversão reparou um polígono auto-interseccionado em geometria válida.

    ![Exemplo de um polígono auto-interseccionado reparado](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* A imagem abaixo mostra como o serviço de Conversão arrancou o primeiro e último vértice de um PolyLine não fechado para criar um PolyLine fechado, onde o primeiro e último vértice estavam a menos de 1 mm de distância.  

    ![Exemplo de uma PoliLine estalada](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* A imagem abaixo mostra como, numa camada que suporta apenas PoliLines fechados, o serviço de Conversão reparou vários PoliLines não fechados. Para evitar descartar os PoliLines não fechados, o serviço combinou-os num único PolyLine fechado.

    ![Exemplo de polilinas não fechadas combinadas num único PolyLine fechado](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*Como corrigir automaticamenteRepairPerformed*

Para corrigir um aviso **automáticoRepairPerformed,** tome as seguintes ações:

1. Inspecione a geometria de cada aviso e o texto de aviso específico.
2. Determine se a reparação automatizada está correta.
3. Se a reparação estiver correta, continue. Caso contrário, vá ao ficheiro de design e resolva o aviso manualmente.

>[!TIP]
>Para suprimir um aviso no futuro, faça alterações ao desenho original de modo a que o desenho original corresponda ao desenho reparado.

## <a name="manifest-warnings"></a>Manifestos avisos

### <a name="redundantattribution"></a>**distribuição redundante**

#### <a name="description-for-redundantattribution"></a>*Descrição para distribuição redundante*

O aviso **redundanteAttribution** ocorre quando o manifesto contém propriedades de objetos redundantes ou conflituosas.

#### <a name="examples-for-redundantattribution"></a>*Exemplos para distribuição redundanteAttribution*

* O snippet JSON abaixo contém dois ou mais `unitProperties` objetos com o mesmo `name` .

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* No corte JSON abaixo, dois ou mais `zoneProperties` objetos têm o mesmo `name` .

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*Como corrigir distribuição redundante*

Para corrigir um aviso de *atribuição de anúncios de pré-distribuição * redundante,* remova propriedades de objetos redundantes ou conflituosos.

### <a name="manifestwarning"></a>**manifesto Aconsesendo**

#### <a name="description-for-manifestwarning"></a>*Descrição para manifestoAconseca*

Um **manifesto O aviso de aviso** ocorre quando o manifesto contém unidades ou objetos de zonaproperados que não são bem-alimentados durante a conversão.

#### <a name="examples-for-manifestwarning"></a>*Exemplos para manifestarAconseseso*

* O manifesto contém um `unitProperties` objeto com um que não tem uma etiqueta correspondente numa `unitName` `unitLabel` camada.

* O manifesto contém um `zoneProperties` objeto com um que não tem uma etiqueta correspondente numa `zoneName` `zoneLabel` camada.

#### <a name="how-to-fix-manifestwarning"></a>*Como corrigir manifestoAwarning*

Para corrigir um **manifesto Atenção,** retire o objeto não utilizado `unitProperties` ou o objeto do manifesto ou adicione uma etiqueta `zoneProperties` unit/zona ao desenho de modo a que o objeto de propriedades seja utilizado durante a conversão.

## <a name="wall-warnings"></a>Avisos de parede

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*Descrição para wallOutsideLevel*

O aviso **wallOutsideLevel** ocorre quando o desenho contém uma geometria de parede fora dos limites de um contorno de nível.

#### <a name="example-for-walloutsidelevel"></a>*Exemplo para wallOutsideLevel*

* A imagem abaixo mostra uma parede interior, em vermelho, fora do limite de nível amarelo.

    ![Exemplo de parede interior fora do limite de nível](./media/drawing-conversion-error-codes/wall-outside-level.png)

* A imagem a seguir mostra uma parede exterior, a vermelho, fora do limite de nível amarelo.

    ![Exemplo de parede exterior fora do limite de nível](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*Como corrigir wallOutsideLevel*

Para fixar um aviso **wallOutsideLevel,** expanda a geometria de nível para incluir todas as paredes. Ou modificar os limites da parede para caber dentro do limite de nível.

## <a name="unit-warnings"></a>Avisos de unidade

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*Descrição para unitOutsideLevel*

Um aviso **unitOutsideLevel** ocorre quando o desenho contém geometria da unidade fora dos limites do contorno de nível.

#### <a name="example-for-unitoutsidelevel"></a>*Exemplo para unitOutsideLevel*

 Na imagem a seguir, a geometria da unidade, a vermelho, excede os limites do limite do nível amarelo.

 ![Exemplo de unidade que excede o limite de nível](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*Como corrigir unidadeOutsideLevel*

Para fixar um aviso **unitOutsideLevel,** expanda o limite de nível para incluir todas as unidades. Ou modificar a geometria da unidade para caber dentro do limite de nível.

### <a name="partiallyoverlappingunit"></a>**parcialmenteOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*Descrição para ParcialmenteOverlappingUnit*

Um aviso **parcialmenteoverlappingUnit** ocorre quando o desenho contém uma geometria unitária parcialmente sobreposta em outra geometria da unidade. O serviço de Conversão descarta unidades sobrepostas.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Cenários de exemplo ParcialmenteOverlappingUnit*

Na imagem a seguir, a unidade sobreposta é realçada a vermelho. `UNIT110` e `HALLWAY` são descartados.

![Exemplo de unidades sobrepostas](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*Como corrigir parcialmente a Unidade de Atalho*

Para corrigir um aviso **parcialmente da Unidade de Adição,** redesenhe cada unidade parcialmente sobreposta de modo a que não se sobreponha a outras unidades.

## <a name="door-warnings"></a>Avisos de porta

### <a name="dooroutsidelevel"></a>**portaOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*Descrição para portaOutsideLevel*

Um aviso **de portaOutsideLevel** ocorre quando o desenho contém uma geometria da porta fora dos limites da geometria de nível.

#### <a name="example-for-dooroutsidelevel"></a>*Exemplo para portaOutsideLevel*

Na imagem a seguir, a geometria da porta, realçada a vermelho, sobrepõe-se ao limite do nível amarelo.

![Exemplo de uma porta sobreposta a um limite de nível](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*Como consertar a portaOutsideLevel*

Para fixar um aviso **de portaOutsideLevel,** redesenhar a geometria da porta de modo a que fique dentro dos limites de nível.

## <a name="zone-warnings"></a>Avisos de zona

### <a name="zonewarning"></a>**zona Aavisar**

#### <a name="description-for-zonewarning"></a>*Descrição para zonaAavisão*

A **zona O aviso de aviso** ocorre quando uma zona não contém uma etiqueta. O serviço de Conversão descarta uma zona que não é rotulada.l

#### <a name="example-for-zonewarning"></a>*Exemplo para zonaAavisão*

A imagem a seguir mostra uma zona que não contém uma etiqueta.

![Exemplo de uma zona não contém um rótulo](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*Como corrigir zonaAavisão*

Para fixar uma **zona Abasteca,** verifique se cada zona tem uma única etiqueta.

## <a name="label-warnings"></a>Avisos de etiqueta

### <a name="labelwarning"></a>*rótuloAavião*

#### <a name="description-for-labelwarning"></a>*Descrição para etiquetaAbassão*

A **etiqueta Aafoca o** aviso de aviso quando o desenho contém etiquetas ambíguas ou contraditórias.

Um **rótulo O aviso** ocorre devido a uma ou mais das seguintes razões:

* Uma etiqueta de unidade não está em nenhuma unidade.
* Uma etiqueta de zona não está em nenhuma zona.
* Uma etiqueta de zona está dentro de duas ou mais zonas.

#### <a name="example-for-labelwarning"></a>*Exemplo para etiquetaAbassão*

A imagem a seguir mostra uma etiqueta que dentro de duas zonas.

![Exemplo de um rótulo dentro de duas zonas ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*Como corrigir a etiquetaAbassão*

Para fixar uma **etiqueta Aavisar,** certifique-se de que:

* Todas as etiquetas da unidade estão dentro das unidades.
* Todas as etiquetas de zona estão dentro das zonas.
* Todas as etiquetas de zona estão em uma e única zona.

## <a name="drawing-package-errors"></a>Erros do pacote de desenho

### <a name="invalidarchiveformat"></a>**invalidAreformat**

#### <a name="description-for-invalidarchiveformat"></a>*Descrição para invalidAreformat*

Ocorre um erro **inválido DoArchiveFormat** quando o pacote de desenho está num formato de arquivo inválido, como GZIP ou 7-Zip. Apenas o formato de arquivo ZIP é suportado.

Um erro **inválido DoArchiveFormat** também ocorrerá se o arquivo ZIP estiver vazio.

#### <a name="how-to-fix-invalidarchiveformat"></a>*Como corrigir invalidArFormat*

Para corrigir um erro **inválido DoArquiteFormat,** verifique se:

* O nome do seu ficheiro de arquivo termina em _.zip_.
* O seu arquivo ZIP contém dados.
* Pode abrir o seu arquivo ZIP.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*Descrição para invalidUserData*

Ocorre um erro **invaliduserData** quando o serviço De conversão não consegue ler um objeto de dados do utilizador a partir do armazenamento.

#### <a name="example-scenario-for-invaliduserdata"></a>*Cenário de exemplo para invalidUserData*

Tentou carregar um pacote de desenho com um `udid` parâmetro incorreto.

#### <a name="how-to-fix-invaliduserdata"></a>*Como corrigir invalidUserData*

Para corrigir um erro **invalidoUserData,** verifique se:

* Forneceu um correto `udid` para o pacote carregado.
* O Azure Maps Creator (Preview) foi ativado para a conta Azure Maps que usou para o upload do pacote Drawing.
* O pedido da API ao serviço de Conversão contém a chave de subscrição da conta Azure Maps que utilizou para o upload do pacote Drawing.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*Descrição para dwgError*

Um **dwgError** quando o pacote de desenho contém um problema com um ou mais ficheiros DWG no arquivo ZIP carregado.

O **dwgError** ocorre quando o pacote de desenho contém um ficheiro DWG que não pode ser aberto porque é inválido ou corrupto.

* Um ficheiro DWG não é um desenho válido do formato de ficheiro AutoCAD DWG.
* Um ficheiro DWG é corrupto.
* Um ficheiro DWG está listado no _manifest.jsficheiro,_ mas falta do arquivo ZIP.

#### <a name="how-to-fix-dwgerror"></a>*Como corrigir dwgError*

Para corrigir um **dwgError,** inspecione a sua _manifest.jsno_ ficheiro confirme que:

* Todos os ficheiros DWG do seu arquivo ZIP são desenhos válidos do formato AutoCAD DWG, abertos cada um em AutoCAD. Remova ou corrija todos os desenhos inválidos.
* A lista de ficheiros DWG na _manifest.jscorresponde aos_  ficheiros DWG no arquivo ZIP.

## <a name="manifest-errors"></a>Erros manifestos

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>Descrição para invalidJsonFormat

Um erro **inválido doJsonFormat** ocorre quando o _manifest.jsno_ ficheiro não pode ser lido.

O _manifest.json_file não pode ser lido devido a erros de formatação ou sintaxe do JSON. Para saber mais sobre como o formato json e a sintaxe, consulte [o formato de troca de dados JavaScript (JSON)](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*Como corrigir inválidoJsonFormat*

Para corrigir um erro **de InvalidJsonFormat,** utilize um linter JSON para detetar e resolver eventuais erros do JSON.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*Descrição para o Campo Desaparecido*

Um erro **desaparecido do Campo Desaguisado** ocorre quando omanifest.js _no_ ficheiro está a faltar dados necessários.

#### <a name="how-to-fix-missingrequiredfield"></a>*Como corrigir o Campo Desaparecido*

Para corrigir um erro **desaparecido do RequiredField,** verifique se o manifesto contém todas as propriedades necessárias. Para obter uma lista completa do objeto manifesto exigido, consulte a [secção manifesto nos requisitos do pacote de desenho](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**MissingManifest**

#### <a name="description-for-missingmanifest"></a>*Descrição para o DesaparecidoManifest*

O erro **desaparecido daManifest** ocorre quando o _manifest.jsno_ ficheiro está em falta no arquivo ZIP.

O erro **desaparecido daManifest** ocorre devido a uma ou mais das seguintes razões:

* O _manifest.jsno_ ficheiro está mal escrito.
* O _manifest.js_ está desaparecido.
* A _manifest.jsnão_ está dentro do diretório de raiz do arquivo ZIP.

#### <a name="how-to-fix-missingmanifest"></a>*Como corrigir o Desaparecimento daManifest*

Para corrigir um erro **daManifest desaparecido,** confirme que o arquivo tem um ficheiro nomeado _manifest.jsao_ nível da raiz do arquivo ZIP.

### <a name="conflict"></a>**conflito**

#### <a name="description-for-conflict"></a>*Descrição do conflito*

O erro **de conflito** ocorre quando o _manifest.jsem_ ficheiro contém informações contraditórias.

#### <a name="example-scenario-for-conflict"></a>*Cenário de exemplo para conflitos*

O serviço de Conversão retornará um erro **de conflito** quando mais de um nível é definido com o mesmo nível ordinal. O seguinte corte JSON mostra dois níveis definidos com o mesmo ordinal.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Como corrigir conflitos*

Para corrigir um erro de **conflito,** inspecione a sua _manifest.js_ e remova qualquer informação conflituosa.

### <a name="invalidgeoreference"></a>**invalidAção**

#### <a name="description-for-invalidgeoreference"></a>*Descrição para invalidAção*

O erro **de invalidação** ocorre quando uma _manifest.jsno_ ficheiro contém uma georeferência inválida.

O erro **de invalidação** ocorre devido a uma ou mais das seguintes razões:

* O utilizador está a georreferenciar um valor de latitude ou longitude que está fora de alcance.
* O utilizador está a georreferenciar um valor de rotação que está fora de alcance.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Cenário de exemplo para invalidAção*

No corte JSON abaixo, a latitude está acima do limite superior.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*Como corrigir a invalidação*

Para corrigir um erro **de invalidação,** verifique se os valores georreferenciados estão dentro do alcance.

>[!IMPORTANT]
>Em GeoJSON, a ordem das coordenadas é longitude e latitude. Se não utilizar a ordem correta, poderá acidentalmente encaminhar uma latitude ou valor de longitude que esteja fora de alcance.

## <a name="wall-errors"></a>Erros de parede

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*Descrição para wallError*

O **wallError** ocorre quando o desenho contém um erro enquanto tenta criar uma função de parede.

#### <a name="example-scenario-for-wallerror"></a>*Cenário de exemplo para wallError*

A imagem a seguir exibe uma característica de parede que não se sobrepõe a nenhuma unidade.

![Exemplo de característica de Parede que não se sobrepõe a nenhuma unidade](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*Como consertar wallError*

Para corrigir um erro **de wallError,** reenvia a parede de modo a que se sobreponha a pelo menos uma unidade. Ou criar uma nova unidade que sobreponha a parede.

## <a name="vertical-penetration-errors"></a>Erros de penetração vertical

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*Descrição para verticalPenetrationError*

O **VerticalPenetrationError** ocorre quando o desenho contém uma característica de penetração vertical ambígua.

O **VerticalPenetrationError** ocorre devido a uma ou mais das seguintes razões:

* O desenho contém uma área de penetração vertical sem áreas de penetração vertical sobrepostas em quaisquer níveis acima ou abaixo dele.
* O pacote de desenho contém um nível com duas ou mais características de penetração vertical nele que ambos se sobrepõem a uma única característica de penetração vertical em outro nível diretamente acima ou abaixo dele.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Cenário de exemplo para verticalPenetrationError*

A imagem abaixo mostra uma área de penetração vertical sem áreas de penetração vertical sobrepostas em níveis acima ou abaixo dela.

![Exemplo de uma penetração vertical 1](./media/drawing-conversion-error-codes/vrt-2.png)

A imagem a seguir mostra uma área de penetração vertical que se sobrepõe a mais de uma área de penetração vertical num nível adjacente.

![Exemplo de uma penetração vertical 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>Como corrigir verticalPenetrationError

Para corrigir um erro **vertical do Artigo de Alteração,** leia como utilizar uma função de penetração vertical no artigo de requisitos de pacote de [desenho.](drawing-requirements.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como usar o visualizador de erros do Azure Maps](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Criador (Pré-visualização) para mapeamento interior](creator-indoor-maps.md)