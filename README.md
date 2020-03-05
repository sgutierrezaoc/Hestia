# Introducció
El Sistema públic de serveis socials constitueix el conjunt de recursos, prestacions, activitats, programes, projectes i equipaments destinats a l'atenció social de la població, de titularitat de l'Administració de la Generalitat, de les entitats locals i d'altres administracions. Aquest Sistema s'organitza en forma de xarxa i diferencia entre serveis socials bàsics i serveis socials especialitzats.

El servei **Hèstia** permet gestionar bona part de les matèries que es porten a terme des d'una Àrea de Serveis Socials Bàsics (ABSS). Està format per una sèrie de mòduls que donen resposta al primer nivell del sistema públic de serveis socials, que tenen caràcter polivalent, comunitari i preventiu i als equips multidisciplinaris que el fan servir com a eina bàsica de treball.

A continuació es descriu les diferents modalitats de consum que s'ofereixen per al servei **Hèstia** i que estan a disposició dels integradors. El Backend de l'**Hèstia** s'integra dins de l'arquitectura de la Plataforma de Col·laboració Interadministrativa (en endavant _PCI_) a mode d'un nou servei accessible a través de la MTI.

Per tant els integradors que vulguin consumir els serveis de l'**Hèstia** ho hauran de fer a través de la missatgeria de la _PCI_ utilitzant l'element `<DatosEspecificos>` d'aquesta. Per a més informació podeu consultar [el document d'integració de la _PCI_](https://www.aoc.cat/knowledge-base/plataforma-de-col-laboracio-administrativa-2)

![ArquitecturaPCI](img/ArquitecturaPCI.png)

Tots els serveis oferts de l'**Hèstia** han de ser consumits en modalitat síncrona i fent ús d'1 operació per petició. El resultat de l'operació s'inclou en l'element de tipus `Respuesta`. 

# Missatgeria específica Hèstia

## Consulta fitxa de persona (HESTIA_CONSULTA_CIUTADA)
Aquesta operació permet consultar totes les dades disponibles d’un ciutadà a partir del seu identificador intern de l’Hèstia, NIF/NIE/Passaport o CIP (targeta sanitària). Es deu indicar obligatòriament el Codi de l'Àrea Bàsica de Serveis Socials on està ubicat el ciutadà i almenys uns dels 3 paràmetres d'identificació. En cas d’indicar més d’un paràmetre, el connector farà la següent priorització:
1.	Si s’especifica l’identificador intern de l’Hèstia, el connector ignorarà la resta de paràmetres indicats i retornarà les dades del ciutadà a partir de l’identificador intern.
2.	Si no s’indica l’identificador intern, i s’indica el NIF (també s’accepta NIE o passaport), el connector ignorarà el CIP indicat i retornarà les dades del ciutadà a partir del NIF (NIE o passaport).
3.	Si no s’indica l’identificador intern i tampoc el NIF, el connector retornarà les dades del ciutadà a partir del seu CIP.
Si no s’indica cap dels paràmetres, el connector retornarà un codi d’error. Si el ciutadà existeix, es retornen totes les seves dades i en cas contrari s’indicarà que no existeix.
S'ha de tenir en compte que actualment només es retornen dades de ciutadans que pertanyin a la pròpia ABSS que fa la consulta.
A continuació es detalla la missatgeria corresponent al bloc de dades específiques. 

### Petició - dades específiques
La missatgeria específica de la petició *HESTIA_CONSULTA_CIUTADA* es troba definida al document [Peticio_DadesEspecifiques_Ciutada.xsd](Peticio_DadesEspecifiques_Ciutada.xsd)

![Peticio_consulta_ciutada.png](img/Peticio_consulta_ciutada.png)

|Element | Descripció|
|------- | ----------|
|PCIConsultaCiutada/codiINE | Codi de l'Àrea Bàsica de Serveis Socials|
|PCIConsultaCiutada/idInternCiutada | Identificador únic del ciutadà dins l’Hèstia|
|PCIConsultaCiutada/NIF | NIF, NIE o Passaport del ciutadà|
|PCIConsultaCiutada/CIP | Codi d’identificació personal assignat per CatSalut|

### Resposta - dades específiques
La missatgeria específica de la resposta *HESTIA_CONSULTA_CIUTADA* es troba definida al document [Resposta_DadesEspecifiques_Ciutada.xsd](Resposta_DadesEspecifiques_Ciutada.xsd)

![Resposta_consulta_ciutada.png](img/Resposta_consulta_ciutada.png)

|Element | Descripció|
|------- | ----------|
|PCIConsultaCiutadaResponse/PCIConsultaCiutadaResult/resultat/codiResultat | -1: La petició no és correcta o no compleix l’esquema|
| | -2: el ciutadà no s’ha trobat. No es retorna l’apartat ciutada|
| | -6: el servei Hèstia no està disponible en aquest moment. La petició s’ha de tornar a enviar més endavant|
| | -9: només es pot accedir a informació de la pròpia ABSS que realitza la consulta|
| | 0: el ciutadà s'ha localitzat correctament. Les seves dades es retornen a l’apartat ciutadà|
|PCIConsultaCiutadaResponse/PCIConsultaCiutadaResult/resultat/descripcio| Missatge descriptiu del resultat de l’operació. En cas d’error es detallen els motius.|

#### ciutada/informacioBasica
![Resposta_consulta_ciutada_informacioBasica1.png](img/Resposta_consulta_ciutada_informacioBasica1.png)

![Resposta_consulta_ciutada_informacioBasica2.png](img/Resposta_consulta_ciutada_informacioBasica2.png)

![Resposta_consulta_ciutada_informacioBasica3.png](img/Resposta_consulta_ciutada_informacioBasica3.png)

![Resposta_consulta_ciutada_informacioBasica4.png](img/Resposta_consulta_ciutada_informacioBasica4.png)

|Element | Descripció|
|------- | ----------|
|//informacioBasica/identificacioCiutada/idInternCiutada | Identificador únic del ciutadà dins l’Hèstia|
|//informacioBasica/identificacioCiutada/idTipusDocumentacio | Tipus de documentació|
| | 0: NIF/NIE|
| | 1: Passaport|
| | 2: Altre|
| | 3: Cap|
| | 4: DNI (sense lletra)|
|//informacioBasica/identificacioCiutada/NIF | Número de document del ciutadà|
|//informacioBasica/identificacioCiutada/NSS | Número de seguretat Social del ciutadà|
|//informacioBasica/identificacioCiutada/CIP | Codi d’identificació personal assignat per CatSalut|
|//informacioBasica/nom | Nom del ciutadà|
|//informacioBasica/cognom1 | Primer cognom del ciutadà|
|//informacioBasica/cognom2 | Segon cognom del ciutadà|
|//informacioBasica/estat/estat | Estat de la fitxa del ciutadà:|
| | 0: Activa|
| | 1: Èxitus<a href="#noteExitus" id="noteExitusref"><sup>1</sup></a>|
|//informacioBasica/estat/dataExitus | Data èxitus|
|//informacioBasica/estat/idUsuariExitus | Identificador del professional que ha executat l’operació d’Èxitus del ciutadà|
|//informacioBasica/estat/nomUsuariExitus | Nom del professional que ha executat l’operació d’Èxitus del ciutadà|
|//informacioBasica/dadesNaixement/nasciturus | Nasciturus<a href="#noteNasciturus" id="noteNasciturusref"><sup>2</sup></a>|
| | 0: No|
| | 1: Sí|
|//informacioBasica/dadesNaixement/dtNaixement | Data de naixement del ciutadà|
|//informacioBasica/dadesNaixement/idPais |	Identificador del [país](Paisos.md) de naixement del ciutadà|
|//informacioBasica/dadesNaixement/descPais | Nom del [país](Paisos.md) de naixement del ciutadà|
|//informacioBasica/dadesNaixement/idProvincia | Identificador de la [província](Provincies.md) de naixement del ciutadà|
|//informacioBasica/dadesNaixement/descProvincia | Nom de la [província](Provincies.md) de naixement del ciutadà|
|//informacioBasica/dadesNaixement/idMunicipi | Identificador del [municipi](Municipis.md) de naixement del ciutadà|
|//informacioBasica/dadesNaixement/descMunicipi | Nom del [municipi](Municipis.md) de naixement del ciutadà|
|//informacioBasica/dadesContacte/telefon1 | Telèfon principal del ciutadà|
|//informacioBasica/dadesContacte/telefon2 | Telèfon secundari del ciutadà|
|//informacioBasica/dadesContacte/email | Correu-e del ciutadà|
|//informacioBasica/Sexe | Gènere del ciutadà:|
| | H: Home|
| | D: Dona|
|//informacioBasica/adrecaResidencia/transeunt | Transeünt:|
| | 0: No|
| | 1: Sí|
|//informacioBasica/adrecaResidencia/idRegim | Règim de tinença:|
| | 0: No consta|
| | 1: Propietaris / Accès propietat|
| | 2: Llogaters|
| | 3: Habitatge Gratuït|
| | 4: Alberg,pensió,hotel,residència|
| | 6: Relloguer|
| | 7: Règim de masover|
| | 8: Ocupació il.legal|
| | 9: NS/NC|
| | 10: Lloguer sense contracte|
| | 11: Relloguer lloguer|
| | 12: Relloguer propietat|
|//informacioBasica/adrecaResidencia/idProvincia | Identificador de la [província](Provincies.md) on resideix el ciutadà|
|//informacioBasica/adrecaResidencia/descProvincia | Descripció de la [província](Provincies.md) on resideix el ciutadà|
|//informacioBasica/adrecaResidencia/idMunicipi | Identificador del [municipi](Municipis.md) on resideix del ciutadà|
|//informacioBasica/adrecaResidencia/descMunicipi | Nom del [municipi](Municipis.md) on resideix del ciutadà|
|//informacioBasica/adrecaResidencia/localitatBarri | Localitat o barri on resideix el ciutadà|
|//informacioBasica/adrecaResidencia/CP | Codi postal de l’adreça de residència del ciutadà|
|//informacioBasica/adrecaResidencia/idTipusVia | [Tipus de via](TipusVia.md) on resideix el ciutadà|
|//informacioBasica/adrecaResidencia/nomVia | Nom de la via on resideix el ciutadà|
|//informacioBasica/adrecaResidencia/numero | Número de la via on resideix el ciutadà|
|//informacioBasica/adrecaResidencia/bloc | Bloc de residència|
|//informacioBasica/adrecaResidencia/escala | Escala de residència|
|//informacioBasica/adrecaResidencia/pis | Pis de residència|
|//informacioBasica/adrecaResidencia/porta | Porta de residència|
|//informacioBasica/adrecaResidencia/idTipusConstruccio | Tipus de construcció de l’habitatge:|
| | 1: Pis|
| | 2: Casa|
| | 3: Mas|
| | 4: Vivenda col.lectiva|
| | 5: Barraca/Caravana|
| | 6: NS/NC|
| | 7: ESTUDI|
|//informacioBasica/adrecaResidencia/idTipusConstruccio | Tipus de construcció de l’habitatge:|
|//informacioBasica/nucliConvivencia/idTipusFamilia	| Tipus de família:|
| | 0: No consta|
| | 1: Unipersonal|
| | 2: Sense nucli|
| | 3: Monoparental|
| | 4: Nuclear|
| | 5: Extensa o ampliada|
| | 6: Múltiple|
| | 7: Reconstituïda|
|//informacioBasica/nucliConvivencia/idNucliConvivencia	| Tipus de nucli de convivència:|
| | 0: No consta|
| | 1: Viu sol|
| | 2: Comparteix pis|
| | 3: Viu amb família|
| | 4: Viu amb amics|
| | 5: Centre o residència|
|//informacioBasica/nucliConvivencia/numMembres | Número de membres del nucli de convivència|
|//informacioBasica/nucliConvivencia/numMenors | Número de menors del nucli de convivència|
|//informacioBasica/nucliConvivencia/numFills | Número de fills del ciutadà|
|//informacioBasica/nucliConvivencia/numACarrec | Número de persones dependents a càrrec del ciutadà|
|//informacioBasica/dependencia/dependent | Dependent:|
| | 0: No|
| | 1: Sí|
|//informacioBasica/dependencia/grauDependencia	| Grau i nivell de dependència:|
| | 0: No consta|
| | 1: GRAU I - Nivell 1|
| | 2: GRAU II - Nivell 1|
| | 3: GRAU III - Nivell 1|
| | 4: GRAU I - Nivell 2|
| | 5: GRAU II - Nivell 2|
| | 6: GRAU III - Nivell 2|
| | 7: GRAU I|
| | 8: GRAU II|
| | 9: GRAU III|
|//informacioBasica/dependencia/expeDP | Número d’expedient de dependència|
|//informacioBasica/discapacitat/discapacitat | Discapacitat:|
| | 0: Sense discapacitat|
| | 1: Amb discapacitat|
|//informacioBasica/discapacitat/grauDiscapacitat | Grau de discapacitat (en percentatge)|
|//informacioBasica/idEstatCivil | Identificador de l’estat civil del ciutadà:|
| | 1: No consta|
| | 2: Parella estable|
| | 3: Casat/ada|
| | 4: Divorciat/ada|
| | 5: Separat/ada de fet|
| | 6: Solter/a|
| | 7: Vidu/a|
| | 8: Separat/ada de dret|
|//informacioBasica/consentiment | Consentiment:|
| | 0: el ciutadà no ha signat el consentiment d’utilització de dades personals|
| | 1: el ciutadà ha signat el consentiment d’utilització de dades personals|
|//informacioBasica/observacions | Observacions|
|//informacioBasica/dadesRegistre/dataAlta | Data i hora d’alta del ciutadà a l’Hèstia|
|//informacioBasica/dadesRegistre/idUsuariAlta | Identificador del professional que va donar d’alta el ciutadà a l’Hèstia|
|//informacioBasica/dadesRegistre/nomUsuariAlta | Nom del professional que va donar d’alta el ciutadà a l’Hèstia|
|//informacioBasica/dadesRegistre/dataModificacio | Data i hora de la darrera modificació de la fitxa del ciutadà|
|//informacioBasica/dadesRegistre/idUsuariModificacio | Identificador del darrer professional que va modificar la fitxa del ciutadà|
|//informacioBasica/dadesRegistre/nomUsuariModificacio | Nom del darrer professional que va modificar la fitxa del ciutadà|
|//informacioBasica/dadesRegistre/idUsuariResponsable | Identificador del professional referent assignat al ciutadà|
|//informacioBasica/dadesRegistre/nomUsuariResponsable | Nom del professional referent assignat al ciutadà|

<a id="noteExitus" href="#noteExitusref"><sup>1</sup></a> Terme latí que s'utilitza en medicina per tancar les històries clíniques d'aquells pacients en els quals l'enfermetat ha finalitzat amb la mort.

<a id="noteNasciturus" href="#noteNasciturusref"><sup>2</sup></a> Terme jurídic per designar al ser humà des de que és concebut fins al seu naixement. Es refereix per tant al ser humà concebut i no nascut.

#### ciutada/informacioAmpliada

![Resposta_consulta_ciutada_informacioBasica1.png](img/Resposta_consulta_ciutada_informacioBasica1.png)

![Resposta_consulta_ciutada_informacioBasica2.png](img/Resposta_consulta_ciutada_informacioBasica2.png)

|Element | Descripció|
|------- | ----------|
|//informacioAmpliada/activitatLaboral/idActivitatLaboral | Identificador de l’[activitat laboral](ActivitatLaboral.md) del ciutadà|
|//informacioAmpliada/activitatLaboral/permisTreball | Permís de treball:|
| | 0: el ciutadà no disposa de permís de treball|
| | 1: el ciutadà disposa de permís de treball|
|//informacioAmpliada/activitatLaboral/dataCaducitatPermis | Data de caducitat del permís de treball|
|//informacioAmpliada/activitatLaboral/idMunicipi |	Identificador del [municipi](Municipis.md) on treballa el ciutadà|
|//informacioAmpliada/activitatLaboral/descMunicipi | Nom del municipi on treballa el ciutadà|
|//informacioAmpliada/activitatLaboral/idSectorLaboral | Identificador del sector laboral del ciutadà:|
| | 0: No consta|
| | 1: Sector primari|
| | 2: Sector secundari|
| | 3: Sector Terciari|
|//informacioAmpliada/activitatLaboral/ocupacioComptePropi | Ocupació per compte propi:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/activitatLaboral/ocupacioCompteAltre | Ocupació per compte d’altre:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/activitatLaboral/ingressos | Import de la font d’ingressos del ciutadà|
|//informacioAmpliada/dadesImmigrants/idSituacioLegal | Identificador de la situació legal del ciutadà:|
| | 0: No consta|
| | 1: Refugiats|
| | 2: Residencia sense permís de treball|
| | 3: Residència en tràmit|
| | 4: Expedient d'expulsió|
| | 5: Es desconeix|
| | 6: Irregular|
| | 7: Residencia i treball|
| | 8: Règim comunitari|
| | 9: Expedient de retornat|
| | 10: Residència llarga durada|
| | 11: Nacionalitat espanyola reconeguda|
|//informacioAmpliada/dadesImmigrants/dataArribadaEspanya | Data d’arribada a Espanya del ciutadà|
|//informacioAmpliada/dadesImmigrants/dataArribadaMunicipi | Data d’arribada al municipi del ciutadà|
|//informacioAmpliada/dadesImmigrants/dataCaducitatNIE | Data de caducitat del NIE del ciutadà|
|//informacioAmpliada/dadesImmigrants/pendentReagrupament/pares | El ciutadà està pendent de reagrupar els pares:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/dadesImmigrants/pendentReagrupament/fills | El ciutadà està pendent de reagrupar els fills:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/dadesImmigrants/pendentReagrupament/conjuge | El ciutadà està pendent de reagrupar el cònjuge:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/idHabilitatComunicativa | Identificador de l’habilitat comunicativa del ciutadà:|
| | 0: No consta|
| | 1: Bona capacitat de comunicació|
| | 2: Nul·la capacitat de comunicació|
| | 3: Dificultats de comunicació|
|//informacioAmpliada/estudisIdiomes/idLlenguaMaterna | Identificador de la [llengua materna](LlenguaMaterna.md) del ciutadà|
|//informacioAmpliada/estudisIdiomes/descLlenguaMaterna | Nom de la llengua materna del ciutadà|
|//informacioAmpliada/estudisIdiomes/idiomes/idioma/idioma | Nom de l’idioma|
|//informacioAmpliada/estudisIdiomes/idiomes/idioma/enten | El ciutadà entén l’idioma:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/idiomes/idioma/parla | El ciutadà parla l’idioma:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/idiomes/idioma/llegeix | El ciutadà llegeix l’idioma:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/idiomes/idioma/escriu | El ciutadà escriu l’idioma:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/idNivellFormacio | Identificador del nivell de formació del ciutadà:|
| | 0: No consta|
| | 1: NS/NC|
| | 2: Analfabetisme|
| | 3: Escola especial|
| | 4: Estudis primaris inacabats|
| | 5: ESO / Graduat escolar|
| | 6: Batxillerat, CFGM / Equivalent|
| | 7: Estudis universitaris grau mig|
| | 8: Estudis universitaris superiors|
| | 9: Programes de dep. treball|
|//informacioAmpliada/estudisIdiomes/estudisHomologats | El ciutadà té homologats els seus estudis:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/associacions/pertanyAssociacio | El ciutadà pertany a associacions:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/associacions/tipus | Identificador del tipus d’associació a la que pertany el ciutadà:|
| | 0: Sense assignar|
| | 1: Agrupacions veïnals|
| | 2: Agrupacions esportives|
| | 3: Agrupacions de dones|
| | 4: Agrupacions juvenils|
| | 5: Agrupacions culturals|
| | 6: Comunitats religioses|
| | 7: Sindicats|
|//informacioAmpliada/estudisIdiomes/associacions/nom | Nom de l’associació a la que pertany el ciutadà|
|//informacioAmpliada/estudisIdiomes/suport/ONG | El ciutadà rep el suport d’alguna ONG:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/suport/administracioPublica | El ciutadà rep el suport d’alguna Administració Pública:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/suport/associacionsImmigrants | El ciutadà rep el suport d’alguna associació d’immigrants:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/estudisIdiomes/suport/personesAltres | El ciutadà rep el suport de persones o d’altres:|
| | 0: No|
| | 1: Sí|
|//informacioAmpliada/dadesRegistre/dataAlta | Data i hora d’alta de la fitxa ampliada del ciutadà a l’Hèstia|
|//informacioAmpliada/dadesRegistre/idUsuariAlta | Identificador del professional que va donar d’alta la fitxa ampliada del ciutadà a l’Hèstia|
|//informacioAmpliada/dadesRegistre/nomUsuariAlta | Nom del professional que va donar d’alta la fitxa ampliada del ciutadà a l’Hèstia|
|//informacioAmpliada/dadesRegistre/dataModificacio | Data i hora de la darrera modificació de la fitxa ampliada del ciutadà|
|//informacioAmpliada/dadesRegistre/idUsuariModificacio | Identificador del darrer professional que va modificar la fitxa ampliada del ciutadà|
|//informacioAmpliada/dadesRegistre/nomUsuariModificacio | Nom del darrer professional que va modificar la fitxa ampliada del ciutadà|
|//informacioAmpliada/dadesRegistre/idUsuariResponsable | Identificador del professional referent assignat al ciutadà|
|//informacioAmpliada/dadesRegistre/nomUsuariResponsable | Nom del professional referent assignat al ciutadà|
