# Power_Automate-Salvar_aquivos
Salva automaticamente arquivos em pastas de acordo com o nome
Excel.LaunchExcel.LaunchAndOpenUnderExistingProcess Path: $'''X:\\+ Empresas\\+ Escritorio\\Planilha de Informações Gerais\\Empresas - Informações Gerais.xlsb''' Visible: True ReadOnly: False Instance=> ExcelInstance
IF Domésticas = $'''True''' THEN
    CALL Doméstica
END
Excel.SetActiveWorksheet.ActivateWorksheetByName Instance: ExcelInstance Name: $'''Empresas'''
ON ERROR
    GOTO Fimm
END
Folder.GetFiles Folder: $'''Y:\\++ Matheus Ceribelli\\DP\\%Ano%\\%Mes%\\Empresas\\Não Nomeadas''' FileFilter: $'''*.pdf''' IncludeSubfolders: False FailOnAccessDenied: True SortBy1: Folder.SortBy.NoSort SortDescending1: False SortBy2: Folder.SortBy.NoSort SortDescending2: False SortBy3: Folder.SortBy.NoSort SortDescending3: False Files=> Pasta_PDF
LOOP FOREACH Arquivo_PDF IN Pasta_PDF
    Text.SplitText.SplitWithDelimiter Text: Arquivo_PDF CustomDelimiter: $'''(?<=\\d\\W)(\\D*)(?=\\s[\\d.\\s]*-)''' IsRegEx: True Result=> TextList
    Pdf.ExtractTextFromPDF.ExtractText PDFFile: Arquivo_PDF DetectLayout: False ExtractedText=> Texto_PDF
    IF Contains(Texto_PDF, $'''Guia do FGTS Digital''', True) THEN
        CALL 'Re FGTS Digital'
    END
    IF Contains(Texto_PDF, $'''Relação de Trabalhadores''', True) THEN
        CALL 'FGTS Digital'
    END
    IF Contains(Texto_PDF, $'''PROGRAMAÇÃO DE FÉRIAS''', False) THEN
        CALL 'Programação de férias'
    END
    IF Contains(Texto_PDF, $'''Assinatura do Funcionário''', False) THEN
        CALL 'Holerite Mensal'
    END
    IF Contains(Texto_PDF, $'''EXTRATO MENSAL''', False) THEN
        CALL 'Extrato Mensal'
    END
    IF Contains(Texto_PDF, $'''Recibo de Entrega da Declaração de Débitos e Créditos Tributários Federais Previdenciários - DCTFWeb''', False) THEN
        CALL 'Recibo de Entrega DCTFWeb'
    END
    IF Contains(Texto_PDF, $'''Documento de Arrecadação''', True) THEN
        CALL 'Darf Unificado'
    END
    IF Contains(Texto_PDF, $'''pluxee refeição''', True) THEN
        CALL 'Relatório vale refeição'
    END
    IF Contains(Texto_PDF, $'''pluxee alimentação''', True) THEN
        CALL 'Relatório vale refeição'
    END
    IF Contains(Texto_PDF, $'''pluxee Combustivel''', True) THEN
        CALL 'Relatório Auxilio Combustivel'
    END
    Variables.ClearList List: CNPJ
    Variables.ClearList List: Codigo
    Variables.ClearList List: Data
END
Excel.CloseExcel.Close Instance: ExcelInstance
LABEL Fimm
