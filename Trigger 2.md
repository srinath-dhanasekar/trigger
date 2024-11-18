# Create field called "Count of Contacts" on Account Object. When we add the Contacts for that Account then count will populate in the field on Account details page. When we delete the Contacts for that Account, then Count will update automatically.

## Trigger
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
                caseStudy2_PopulateCountOfContacts(TriggerNew);
            }
            When AFTER_UPDATE
            {
                caseStudy2_PopulateCountOfContacts(TriggerNew);
            }
            When BEFORE_DELETE
            {
                
            }
            When AFTER_DELETE
            {
                caseStudy2_PopulateCountOfContacts(TriggerOld);
            }
            When AFTER_UNDELETE
            {
                
            }
        }
    }
    
    public void caseStudy2_PopulateCountOfContacts(List<Contact> conList)
    {
        Set<Id> accIds = new Set<Id>();
        
        for(Contact conRec:conList)
        {
            if(Trigger.isUpdate && conRec.AccountId != TriggerOldMap.get(conRec.Id).AccountId)
            {
                accIds.add(TriggerOldMap.get(conRec.Id).AccountId);
                accIds.add(conRec.AccountId);
            }
            else if(conRec.AccountId != Null)
            {
                accIds.add(conRec.AccountId);
            }
        }
        
        if(accIds.size()>0)
        {
            Map<Id, AggregateResult> agResult = new Map<Id, AggregateResult>([SELECT COUNT(Id)countOfContact, AccountId Id
                                                                              FROM Contact
                                                                              WHERE AccountId IN: accIds
                                                                              GROUP BY AccountId]);
            
            List<Account> accList = new List<Account>();
            
            for(Id recId:accIds)
            {
                Account acc = new Account();
                acc.Id = recId;
                
                if(agResult.containsKey(recId))
                {
                    acc.Count_of_Contacts__c=(Integer)agResult.get(recId).get('countOfContact');
                }
                else
                {
                    acc.Count_of_Contacts__c=0;
                }
                accList.add(acc);
            }
            
            if(accList.size()>0)
            {
                Update accList;
            }
        } 
    }
    
}
