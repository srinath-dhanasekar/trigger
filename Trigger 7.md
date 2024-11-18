# Create a text field called Oppr_Lineltems_ProductCode_C in opportunity object. Whenever an opportunity line item is getting created/updated/deleted associate its product code with its opportunity. Note: Oppr_Lineltems_ProductCode_C can be comma separated values.

# Trigger

trigger OpportunityLineItemTrigger on OpportunityLineItem (before insert, after insert, before update, after update, before delete, after delete, after undelete) {
    
    OpportunityLineItemTriggerHandler obj = new  OpportunityLineItemTriggerHandler();
    obj.doAction();

}

# Handler using Switch

public class OpportunityLineItemTriggerHandler {
    
    List<OpportunityLineItem> TriggerOld;
    List<OpportunityLineItem> TriggerNew;
    Map<Id, OpportunityLineItem> TriggerOldMap;
    Map<Id, OpportunityLineItem> TriggerNewMap;
    
    public OpportunityLineItemTriggerHandler()
    {
        TriggerOld = (List<OpportunityLineItem>) Trigger.old;
        TriggerNew = (List<OpportunityLineItem>) Trigger.new;
        TriggerOldMap = (Map<Id, OpportunityLineItem>) Trigger.oldmap;
        TriggerNewMap = (Map<Id, OpportunityLineItem>) Trigger.newmap;
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
                caseStudy7_OppLineItemCode(TriggerNew);
            }
            When AFTER_UPDATE
            {

            }
            When BEFORE_DELETE
            {
                
            }
            When AFTER_DELETE
            {
                caseStudy7_OppLineItemCode(TriggerOld);
            }
            When AFTER_UNDELETE
            {

            }
        }
    }
    
    public void caseStudy7_OppLineItemCode(List<OpportunityLineItem> oppLineList)
    {
        Set<Id> oppIds = new Set<Id>();
        
        for(OpportunityLineItem rec:oppLineList)
        {
            if(rec.OpportunityId != NULL)
            {
                OppIds.add(rec.OpportunityId);
            }
        }
  
        if(oppIds.size()>0)
        {
            List<OpportunityLineItem> oppLineIdList = [SELECT Id, OpportunityId 
                                                       FROM OpportunityLineItem
                                                       WHERE OpportunityId IN: OppIds];        
            
            Map<Id, List<Id>> oppLineIdsMap = new Map<Id, List<Id>>();
            
            for(OpportunityLineItem rec : oppLineIdList)
            {
                if(!oppLineIdsMap.containsKey(rec.OpportunityId))
                {
                    oppLineIdsMap.put(rec.OpportunityId, new List<Id>{rec.Id});
                }
                else
                {
                    oppLineIdsMap.get(rec.OpportunityId).add(rec.Id);
                }
            }
            
            List<Opportunity> oppList = new List<Opportunity>();

            for(Id oppId : oppLineIdsMap.keySet())
            {
                Opportunity opp = new Opportunity();
                opp.Id = oppId;
                opp.Oppr_LineItem_ProductCode__c = String.join(oppLineIdsMap.get(oppId),',');
                oppList.add(opp);
            }
            
            if(oppList.size()>0)
            {
                Update oppList;
            }
        }
    }
    
}
