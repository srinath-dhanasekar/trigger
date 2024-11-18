# Create the field called "Contact Relationship" checkbox on the Contact Object and Create the related object called "Contact Relationship" which is related list to the Contacts.(Lookup Relationship).
    a. When we create contact by checking Contact Relationship checkbox, then Contact Relationship will be created automatically for that     contact.
    b. When we change the Owner of the Contact Relationship, then the Owner name will be automatically populated in the Contact Relationship Name field.
    c. when we delete the Contact, Then Contact Relationship will be deleted automatically
    d. when we undelete the Contact, then Contact Relationship will be undeleted automatically

# Trigger

trigger ContactTrigger on Contact (before insert, after insert, before update, after update, before delete, after delete, after undelete) {

    ContactTriggerHandler obj = new ContactTriggerHandler();
    obj.doAction();
}

## Handler using Switch

public class ContactTriggerHandler {
    
    List<Contact> TriggerOld;
    List<Contact> TriggerNew;
    Map<Id, Contact> TriggerOldMap;
    Map<Id, Contact> TriggerNewMap;
    
    Static List<Contact_Relationship__c> conRelList = new List<Contact_Relationship__c>();
    
    public ContactTriggerHandler() 
    {
        TriggerOld = (List<Contact>)Trigger.old;
        TriggerNew = (List<Contact>)Trigger.new;
        TriggerOldMap = (Map<Id, Contact>)Trigger.oldmap;
        TriggerNewMap = (Map<Id, Contact>)Trigger.newmap;
    }
    
    public void doAction()
    {
        Switch on trigger.OperationType
        {
            When BEFORE_INSERT
            {
                
            }
            When BEFORE_UPDATE
            {
                
            }
            When AFTER_INSERT
            {
                caseStudy5_CreateContactRelationship();
            }
            When AFTER_UPDATE
            {

            }
            When BEFORE_DELETE
            {
                caseStudy5_DeleteContactRelationship_BeforeDelete();
            }
            When AFTER_DELETE
            {
                caseStudy5_DeleteContactRelationship_AfterDelete();
            }
            When AFTER_UNDELETE
            {
                caseStudy5_UndeleteContactRelationship();
            }
        }
    }

    public void caseStudy5_CreateContactRelationship()
    {
        List<Contact_Relationship__c> conRelList = new List<Contact_Relationship__c>();
        
        for(Contact conRec:TriggerNew)
        {
            if(conRec.Contact_Relationship__c)
            {
                Contact_Relationship__c conRelRec = new  Contact_Relationship__c();
                
                conRelRec.Name = conRec.LastName+' Related Relationship';
                conRelRec.Contact_Name__c = conRec.Id;
                conRelList.add(conRelRec);	
            }
        }
        
        if(conRelList.size()>0)
        {
            Insert conRelList;
        }        
    }
    
    public void caseStudy5_DeleteContactRelationship_BeforeDelete()
    {
        conRelList = [SELECT Id, Name 
                      FROM Contact_Relationship__c 
                      WHERE Contact_Name__c IN: TriggerOld];
    }
    
    public void caseStudy5_DeleteContactRelationship_AfterDelete()
    {
        if(conRelList.size()>0)
        {
            Delete conRelList;
        }
    }
    
    public void caseStudy5_UndeleteContactRelationship()
    {
        Set<Id> conIds = new Set<Id>();
        
        for(Contact conRec : TriggerNew)
        {
            conIds.add(conRec.Id);
        }
        
        if(conIds.size()>0)
        {
            List<Contact_Relationship__c> deletedConRelList = [SELECT Id 
                                                               FROM Contact_Relationship__c 
                                                               WHERE Contact_Name__c IN :conIds AND IsDeleted = true ALL ROWS];
            
            if (deletedConRelList.size() > 0) 
            {
                undelete deletedConRelList;
            }
        }
    }
}

# Trigger

trigger ContactRelationshipTrigger on Contact_Relationship__c (before insert, after insert, before update, after update, before delete, after delete, after undelete) {
    
    ContactRelationshipTriggerHandler obj = new ContactRelationshipTriggerHandler();
    obj.doAction();

}

## Handler

public class ContactRelationshipTriggerHandler {
    
    List<Contact_Relationship__c> TriggerOld;
    List<Contact_Relationship__c> TriggerNew;
    Map<Id, Contact_Relationship__c> TriggerOldMap;
    Map<Id, Contact_Relationship__c> TriggerNewMap;
    
    public ContactRelationshipTriggerHandler() 
    {
        TriggerOld = (List<Contact_Relationship__c>)Trigger.old;
        TriggerNew = (List<Contact_Relationship__c>)Trigger.new;
        TriggerOldMap = (Map<Id, Contact_Relationship__c>)Trigger.oldmap;
        TriggerNewMap = (Map<Id, Contact_Relationship__c>)Trigger.newmap;
    }
    
    public void doAction()
    {
        Switch on trigger.OperationType
        {
            When BEFORE_INSERT
            {
                
            }
            When BEFORE_UPDATE
            {
                CaseStudy5_PopulateOwnerName();
            }
            When AFTER_INSERT
            {

            }
            When AFTER_UPDATE
            {

            }
            When BEFORE_DELETE
            {
                
            }
            When AFTER_DELETE
            {
                
            }
            When AFTER_UNDELETE
            {
                
            }
        }
    }
    
    public void CaseStudy5_PopulateOwnerName()
    {
        Set<Id> ownerId = new Set<Id>();
        
        for(Contact_Relationship__c conRelRec : TriggerNew)
        {
            if(TriggerOldMap.get(conRelRec.Id).OwnerId != conRelRec.OwnerId)
            { 
                ownerId.add(conRelRec.OwnerId);
            }
        }
        
        Map<Id, User> userMap = new Map<Id, User>([SELECT Id, Name 
                                                   FROM User 
                                                   WHERE Id IN: ownerId]);
        
        for(Contact_Relationship__c conRelRec : TriggerNew)
        {
            if(TriggerOldMap.get(conRelRec.Id).OwnerId != conRelRec.OwnerId)
            { 
                conRelRec.Name = userMap.get(conRelRec.OwnerId).Name;
            }
        }
    }
    
}
