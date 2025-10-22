# diagrama

flowchart LR
  %% Swimlanes por actor
  subgraph NEGOCIO["🟦 Negocio (Empresa Cliente)"]
    N1[Configura necesidad:\n- Centros de trabajo\n- Subcategorías (etiquetas)\n- Correos destino]
  end

  subgraph UI["🟩 SuAporte / WIEN (UI)"]
    U1[Cargar CSV Códigos de Asociación\n(código,nombre,correo1..n)]
    U2[Cargar CSV Empleado↔Código\n(tipo_doc,num_doc,código)]
    U3[Ejecutar Envío\n(período, con/sin valores,\n filtros opcionales)]
    U4[Reenvío Manual / Histórico]
    U5[Panel de Trazabilidad\n(estados, logs, descargas)]
  end

  subgraph BE["🟧 Backend SuAporte"]
    B1[Validar CSVs y Persistir\n- association_code\n- employee_association]
    B2[Crear Delivery Jobs\npor código de asociación]
    B3[Orquestar Descargas\nAutoliquidación / Valores x Admin]
    B4[Unir PDFs → 1 por Código\nRenombrar y Guardar]
    B5[Enviar Emails\n(≥1 correo obligatorio)]
    B6[Actualizar Estado y Auditoría\n(PENDING/RUNNING/DONE/FAILED)]
    B7[Reintentos y Reprocesos\n idempotentes]
  end

  subgraph AP["🟨 Proveedor (Aportes en Línea / Fuente)"]
    A1[(Autoliquidación PDF)]
    A2[(Valores por Administradora PDF)]
  end

  subgraph DEST["🟪 Destinatarios"]
    D1[Correos configurados por Código]
    D2[Repositorio de Archivos\n(Blob/S3) con enlaces]
  end

  N1 --> U1 --> B1
  U2 --> B1 --> B2
  U3 --> B2
  B2 --> B3
  B3 -->|descarga| A1
  B3 -->|descarga| A2
  A1 --> B4
  A2 --> B4
  B4 --> D2
  B4 --> B5
  B5 --> D1
  B5 --> B6
  U5 --> B6
  U4 --> B7
  B7 --> B3
