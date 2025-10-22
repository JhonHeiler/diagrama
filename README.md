classDiagram
  class WorkCenter {
    +id
    +name
    +external_code
    +created_at
  }

  class AssociationCode {
    +id
    +code
    +name
    +emails : string[]  // al menos 1
    +status
    +created_at
  }

  class Employee {
    +id
    +doc_type
    +doc_number
    +full_name
    +work_center_id
  }

  class EmployeeAssociation {
    +id
    +employee_id
    +association_code_id
    +valid_from
    +valid_to
  }

  class DeliveryJob {
    +id
    +period        // YYYY-MM
    +association_code_id
    +mode          // WITH_VALUES | NO_VALUES
    +status        // PENDING | RUNNING | DONE | FAILED
    +file_url
    +error_msg
    +created_at
    +updated_at
  }

  class AuditLog {
    +id
    +entity
    +entity_id
    +action
    +performed_by
    +timestamp
    +details
  }

  Employee "1" --> "1" WorkCenter : belongs to
  Employee "1" -- "0..*" EmployeeAssociation : memberships
  AssociationCode "1" -- "0..*" EmployeeAssociation : groups
  AssociationCode "1" -- "0..*" DeliveryJob : deliveries
  DeliveryJob "1" -- "0..*" AuditLog : traces
