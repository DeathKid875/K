local DataStoreService = game:GetService("DataStoreService")
local RollbackData = DataStoreService:GetDataStore("RollbackInventory") -- สร้าง DataStore

game.Players.PlayerAdded:Connect(function(player)
    local userId = player.UserId
    local savedInventory = RollbackData:GetAsync(userId)

    if savedInventory then
        -- โหลดไอเท็มจาก Rollback Data
        player:SetAttribute("Inventory", savedInventory)
        print(player.Name .. "'s inventory has been rolled back.")
    else
        player:SetAttribute("Inventory", {}) -- ถ้าไม่มีข้อมูล ให้เริ่มต้นเป็น Inventory ว่างเปล่า
    end
end)

game.Players.PlayerRemoving:Connect(function(player)
    local userId = player.UserId
    local inventory = player:GetAttribute("Inventory")
    
    if inventory then
        RollbackData:SetAsync(userId, inventory) -- บันทึกข้อมูลก่อนออกเกม
    end
end)

game.ReplicatedStorage.SaveRollback.OnServerEvent:Connect(function(player)
    local inventory = player:GetAttribute("Inventory")
    
    if inventory then
        RollbackData:SetAsync(player.UserId, inventory) -- บันทึก Inventory ปัจจุบัน
        player:SetAttribute("RollbackEnabled", true) -- เปิดสถานะ Rollback
        print(player.Name .. " saved their inventory for rollback.")
    end
end)

game.ReplicatedStorage.RemoveRollback.OnServerEvent:Connect(function(player)
    RollbackData:RemoveAsync(player.UserId) -- ลบข้อมูล Rollback
    player:SetAttribute("RollbackEnabled", false)
    print(player.Name .. " removed rollback data.")
end)
